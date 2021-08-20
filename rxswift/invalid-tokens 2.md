# [HowTo] RxSwift + Handling Invalid Tokens

When I was developing the application to be uploaded to the App Store this time, I faced a different problem than usual.
It didn't matter if API requests were restricted to use by myself, but since it was an app to be uploaded to the App Store, if the number of users increases, the free API Plan will not be able to accommodate users.

However, it was too expensive to spend a lot of money to use the API, so after several considerations, I decided to enter several free API keys.

The principle is this. As in the above site, if an API request is made, but encounters an error such as 401, the request is made with the next API key.  This method is expected to accommodate up to 50 users.

```swift
//  APIManager.swift

import UIKit
import RxSwift
import RxCocoa

class APIManager {
    
    static var shared: APIManager = APIManager()
    
    func makeResource(selectedLanguagesCode: String) -> (String) -> Resource<ArticleResponse> {
        { apiKey in
            Resource<ArticleResponse>(url: URL(string: "https://newsapi.org/v2/top-headlines?country=\(selectedLanguagesCode)&sortBy=%20popularity&apiKey=\(apiKey)")!)
        }
    }
    
    func produceApiKey(apiKeys: [String]) -> Observable<String> {
        var index = 0
        return Observable.create { observer in
            observer.onNext(apiKeys[index % apiKeys.count])
            observer.onCompleted()
            index += 1
            return Disposables.create()
        }
    }
}
```

```swift
private func populateNews() {
    apiManager.produceApiKey(apiKeys: apiKey)
        .map(apiManager.makeResource(selectedLanguagesCode: languageCode))
        .flatMap(URLRequest.load(resource:))
        .retry(apiKey.count + 1)
        .subscribe(onNext: { articleResponse in
            let articles = articleResponse.articles
            self.articleListVM = ArticleListViewModel(articles)
            
            DispatchQueue.main.async {
                self.tableView.reloadData()
            }
        })
        .disposed(by: disposeBag)
}
```

## References

[RxSwift and Handling Invalid Tokens](https://danielt1263.medium.com/retrying-a-network-request-despite-having-an-invalid-token-b8b89340d29)
