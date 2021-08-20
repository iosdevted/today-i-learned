# [HowTo] RxSwift + RESTful API + MVVM

## URL API REQ

### map을 이용하여 원소에 접근하여 최종적으로 URLRequest획득

```swift
DispatchQueue.global(qos: .default).async { [weak self] in
    let response = Observable.from([repo])
    .map { urlString -> URL in
      return URL(string: "https://api.github.com/repos/\(urlString)/events")!
    }.map { url -> URLRequest in
      return URLRequest(url: url)
    }
}
```

### flatMap을 이용하여 새로운 Observable생성 👍👍

 *map이 아닌 flatMap을 사용하는 이유

- 비동기적으로 작동하는 observable을 통해 효과적으로 "대기"할수 있도록 함(==그 동안 다른 연결들은 계속 작동되게끔) (웹의 response를 기다리기 위함)

- 문자열 또는 숫자 array들의 observable 같이 일시적으로 요소를 방출하고 완료된 observable들을 flatten

```swift
.flatMap { request -> Observable<(response: HTTPURLResponse, data: Data)> in
     return URLSession.shared.rx.response(request: request)
 }
```

- RxCocoa의 URLSession객체 내에 있는 response(request:)를 이용(RES를 서버로 부터 받으면 .next이벤트를 한 번 발생)

- response(request:)의 반환 값 : 앱이 웹 서버를 통해 full response를 받을 때 마다 Observable<(response: HTTPURLResponse, data:
   Data)>)를 반환

 - flatMap이 REQ이고 그의 반환값이 RES라고 생각

### .share(replay:scope:)

- share이 없을경우 문제 : 위의 코드로 RES를 받게되면 .nextf를 emit할 때 1회성이므로 새로운 subscribed observer들은 .next이벤트를 받지 않음

- replay : replay로 emit한 요소를 가지고 있다가 새로운 subscriber가 생길 때 이를 제공

- scope : .whileConnected (n/w response buffer를 아무도 subscribe하고 있지 않을때까지 가지고 있는 것)
              .forever (n/w response buffer를 계속 가지고 있는 것, 새로운 subscriber는 buffer response를 갖음)

```swift
.share(replay: 1, scope: .whileConnected)
```

### 결론

```swift
DispatchQueue.global(qos: .default).async { [weak self] in
    let response = Observable.from([repo])
    .map { urlString -> URL in
      return URL(string: "https://api.github.com/repos/\(urlString)/events")!
    }.map { url -> URLRequest in
      return URLRequest(url: url)
    }.flatMap { request -> Observable<(response: HTTPURLResponse, data: Data)> in
      return URLSession.shared.rx.response(request: request)
    } .share(replay: 1, scope: .whileConnected)
}
```

## RES

### response observable에 대한 구독을 만들어서 리스폰스 데이터를 객체로 변환

```swift
 response
     .filter { response, _ in
         return 200 ..< 300 ~= response.statusCode
 }
```

### 서버로 부터 받은 JSON 형식의 데이터를 [Event]로 변환

```swift
.map { _, data -> [Event] in
  let decoder = JSONDecoder()
  let events = try? decoder.decode([Event].self, from: data)
  return events ?? []
}
```

### 이벤트 객체가 존재하는 것만 filter

```swift
.filter { objects in
    return !objects.isEmpty
}
```

### 구독 요청

```swift
.subscribe(onNext: { [weak self] newEvents in
        self?.processEvents(newEvents)
      })
        .disposed(by: self?.bag ?? DisposeBag())
```

## 실전

How to use URLSession for the web API calls in reactive way in MVVM architecture.

### JSON

```JSON
{
  "status": "ok",
  "totalResults": 19821,
  "articles": [
    {
      "source": {
        "id": null,
        "name": "Itc.ua"
      },
      "author": "Сергей Кулеш",
      "title": "Українські розробники презентували на IDEX-2021 плаваючий гусеничний броньований гібридний електромобіль «Шторм» [фото, відео]",
      "description": "Команда українського конструктора бронетехніки Олександра Кузнєцова представила на міжнародній оборонній виставці в Об’єднаних Арабських Еміратах IDEX-2021 свою розробку «Шторм». Це гусенична броньована платформа з гібридною тягою, що здатна розвивати швидкіс…",
      "url": "https://itc.ua/news/ukra%d1%97nski-rozrobniki-prezentuvali-na-idex-2021-plavayuchij-gusenichnij-bronovanij-elektromobil-shtorm-foto-video/",
      "urlToImage": "https://i0.wp.com/itc.ua/wp-content/uploads/2021/02/shtorm.jpg",
      "publishedAt": "2021-02-22T11:20:14Z",
      "content": "IDEX-2021 «».\r\n , 140 / .\r\n« «», . 1,5 . 140 / 18 36 . . , , , . , , », .\r\n, . , , . . , .\r\n. . Streit Group ’ .\r\n« . , 65 , . , Tesla. , , . , . . 70% », .\r\n, , . , .\r\n, 5% , .\r\n« . . , . , . , , «»… [+42 chars]"
    },

    ...
```

### Model

```swift
//
//  Article.swift
//  RxNews
//
//  Created by Ted on 2021/02/22.
//
import Foundation

struct ArticleResponse: Decodable {
    let articles: [Article]
}

struct Article: Decodable {
    let title: String
    let publishedAt: String
}
```

### ViewModel

```swift
//
//  ArticleViewModel.swift
//  RxNews
//
//  Created by Ted on 2021/02/22.
//

import Foundation
import RxSwift
import RxCocoa

struct ArticleListViewModel {
    
    let articlesVM: [ArticleViewModel]
}

extension ArticleListViewModel {
    
    init(_ articles: [Article]) {
        self.articlesVM = articles.compactMap(ArticleViewModel.init)
    }
}

extension ArticleListViewModel {
    
    func articleAt(_ index: Int) -> ArticleViewModel {
        return self.articlesVM[index]
    }
}


struct ArticleViewModel {
    
    let article: Article
    
    init(_ article: Article) {
        self.article = article
    }
}

extension ArticleViewModel {
    
    var title: Observable<String> {
        return Observable<String>.just(article.title)
    }
    
    var description: Observable<String> {
        return Observable<String>.just(article.description ?? "")
    }
}
```

### URLRequest+Extension

```swift
//  URLRequest+Extension.swift
//  RxNews
//
//  Created by Ted on 2021/02/22.
//

import Foundation
import RxSwift
import RxCocoa

struct Resource<T: Decodable> {
    let url: URL
}

extension URLRequest {
    
    static func load<T>(resource: Resource<T>) -> Observable<T> {
        return Observable.just(resource.url)
            .flatMap { url -> Observable<Data> in
            //flatMap make events to another observable
                let request = URLRequest(url: url)
                 //a simple way to make data type with RxSwift
                return URLSession.shared.rx.data(request: request)
            }.map { data -> T in
                return try JSONDecoder().decode(T.self, from: data)
            }
    }
}
```

### ViewController

```swift
//
//  NewsTableViewController.swift
//  RxNews
//
//  Created by Ted on 2021/02/21.
//

import UIKit
import RxSwift
import RxCocoa

...
//MARK: - Properties
private var articleListVM: ArticleListViewModel!

...

//MARK: - Helpers
private func populateNews() {
    
    let resource = Resource<ArticleResponse>(url: URL(string: "")!)
    
    URLRequest.load(resource: resource)
        .subscribe(onNext: { articleResponse in
            
            let articles = articleResponse.articles
            self.articleListVM = ArticleListViewModel(articles)
            
            DispatchQueue.main.async {
                self.tableView.reloadData()
            }
            
        }).disposed(by: disposeBag)
}

... 

extension NewsViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.articleListVM == nil ? 0 : self.articleListVM.articlesVM.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: ReuseIdentifier, for: indexPath) as! ArticleTableViewCell
        
        let articleVM = self.articleListVM.articleAt(indexPath.row)
        
        articleVM.title.asDriver(onErrorJustReturn: "")
            .drive(cell.titleLabel.rx.text)
            .disposed(by: disposeBag)
        
        articleVM.description.asDriver(onErrorJustReturn: "")
            .drive(cell.descriptionLabel.rx.text)
            .disposed(by: disposeBag)
        
        return cell
    }
}
```

### Info.plist

If you want to allow HTTP connections to any site, you should use this keys:

```
App Transport Security Settings
    Allow Arbitrary Loads -> Yes
```

### Github Link

[RxNews Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/RxNews)
