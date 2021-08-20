# Network Requests and REST APIs in iOS

매번 똑같은 코드를 복사하고 붙여놓았는데 코딩 면접때 API 관련 질문을 무조건 할 것 같아서 글을 읽어보았다. 

글을 읽으면서 유용하게 생각했던 정보들을 정리해보자면

## Three fundamental types

### class URLSession

Represents on HTTP session. Used to make individual HTTP requests that share configuration and cocking.

### struct URLRequest

Represents on HTTP request. Includes parameters like the URL, the HTTP method, and the HTTP headers.

평소에 get을 사용하기 떄문에 따로 설정해줄 필요가 없다. 다만 RxSwift 경우 URLRequest를 넣어주어여 URLSessionTask에 넣어주어야 사용 가능하다. 

### class URLSessionTask

performs the data transfer for a network request. You don't use this class directly, but one of its subclasses like URLSessionDataTask.

## model을 효과적으로 이용하는 방법

```swift
struct WeatherData: Decodable {
    let name: String
    let main: Main
    let weather: [Weather]
    
    var model: WeatherModel {
        // viewcontrol과 연결할 부분, api에서 fetch를 한 다음, 여기서 바로 데이터를 가공할 수 있다. 
        return WeatherModel(countryName: name,
                            temp: main.temp.toInt(),
                            conditionId: weather.first?.id ?? 0,
                            conditionDescription: weather.first?.description ?? "")
    }
}

struct Main: Decodable {
    let temp: Double
}

struct Weather: Decodable {
    let id: Int
    let main: String
    let description: String
}

struct WeatherModel {
    // 이미 fetch 된 데이터를 이용하기 떄문에 Decodable이 필요없다. 
    // viewcontrol과 연결할 부분, api에서 fetch를 한 다음, 여기서 바로 데이터를 가공할 수 있다. 
    let countryName: String
    let temp: Int
    let conditionId: Int
    let conditionDescription: String
    
    var conditionImage: String {
        switch conditionId {
        case 200...299:
            return "imThunderstorm"
        case 300...399:
            return "imDrizzle"
        case 500...599:
            return "imRain"
        case 600...699:
            return "imSnow"
        case 700...799:
            return "imAtmosphere"
        case 800:
            return "imClear"
        default:
            return "imClouds"
        }
    }
}
```

## CodingKey

CodingKey를 이용하면 model 파일 struct 안에서 JSON 파일과 동일한 이름을 가질 필요가 없다. struct 안에서는 좀 더 직관적인 이름으로 하고 codingKey를 통해 매칭을 해주면 된다.

```swift
struct User {
	let name: String?
	let reputation: Int?
	let profileImageURL: URL?
	var profileImage: UIImage?
}
 
 // CodingKey 부분에만 decondable를 넣어주기 
extension User: Decodable {
	enum CodingKeys: String, CodingKey {
		case reputation
		case name = "display_name"
		case profileImageURL = "profile_image"
	}
}
```

## 까먹을까봐 적는 코드

VIPER 구조에서는 대충 이렇게 적용가능. 진짜 대충 적음 

```swift
enum ServiceError: Error {
    case unknown
    case urlTransformFailed
    case requestFailed(response: HTTPURLResponse, data: Data?)
}

func load(with completion: @escaping (Result<Question, ServiceError>) -> Void) {
    guard let url = URL(string: "") else { return }
    let task = URLSession.shared.dataTask(with: url) { (data, _, error) in
        guard let data = data, error == nil else {
            completion(.failure(ServiceError.unknown))
            return
        }
        
        do {
            let decoded = try JSONDecoder().decode(Question.self, from: data)
            completion(.success(decoded))
        } catch {
            completion(.failure(ServiceError.unknown))
        }
    }
    task.resume()
}

func interactorDidFetchUsers(with result: Result<[User], Error>) {
    switch result {
    case .success(let users):
        view?.update(with: users)
    case .failure:
        view?.update(with: "Something went wrong")
    }
}
```

## rxswift에서는 대충 이렇게

MVVM 구조에서는 이렇게

```swift
//ServiceResult.swift
enum ServiceError: Error {
    case unknown
    case urlTransformFailed
    case requestFailed(response: HTTPURLResponse, data: Data?)
}

//Reactive+Base.swift
// session.rx.data 같은 경우 observable를 반환하기 때문에 single 타입의 extension으로 새로운 함수를 만들어준다. 
extension Reactive where Base: URLSession {
    func dataTask(request: URLRequest) -> Single<Data> {
        return Single.create(subscribe: { observer -> Disposable in
            
            let task = self.base.dataTask(with: request) { (data, response, error) in
                
                guard let response = response as? HTTPURLResponse, let data = data else {
                    observer(.failure(error ?? ServiceError.unknown))
                    return
                }
                
                guard 200..<400 ~= response.statusCode else {
                    observer(.failure(error ?? ServiceError.requestFailed(response: response, data: data)))
                    return
                }
                observer(.success(data))
                
            }
            task.resume()
            return Disposables.create(with: task.cancel)
        })
    }

//  Data+Decode.swift
extension Data {
    func decode<T>(_ type: T.Type, decoder: JSONDecoder? = nil) throws -> T where T: Decodable {
        let decoder = decoder ?? JSONDecoder()
        return try decoder.decode(type, from: self)
    }
}
//  APIService.swift
protocol APIServiceType {
    func fetchData() -> Single<[ArticleContent]>
}

struct APIService: APIServiceType {
    
    //MARK: - Properties
    
    private let session: URLSession
    
    //MARK: - Initialize
    
    init(session: URLSession = URLSession.shared) {
        self.session = session
    }
    
    // MARK: - DataTask
    
    func fetchData() -> Single<[ArticleContent]> {
        let baseUrl = ""
        
        guard let url = URL(string: baseUrl) else { return .error(ServiceError.urlTransformFailed) }
        
        return session.rx.dataTask(request: URLRequest(url: url))
            .map { data throws in
                let articles = try data.decode(Article.self)
                return articles.articles
                
            }
    }
}
//  MainViewModel.swift
let articles: Driver<[ArticleData]>

articles = Observable<Void>
    .merge([viewWillAppear, didTapLeftBarButton, didPulltoRefresh])
    .observe(on: ConcurrentDispatchQueueScheduler(qos: .default))
    .do(onNext: { _ in onNetworking.onNext(true)})
    .flatMap {
        return apiService.fetchData()
            .retry(2)
            .do(onDispose:  { onNetworking.onNext(false) })
            .catch({ error -> Single<[ArticleContent]> in
                onError.onNext(error)
                return .never()
            })
    }
    .map { [ArticleData(model: "", items: $0)] }
    .asDriver(onErrorJustReturn: [])
```

MVC에서는 대충 이렇게

```swift
//  WeatherResult.swift

struct WeatherResult: Codable {
    let main: Weather
}

extension WeatherResult {
    //이런 방식으로 오류가 일어날 경우를 미리 지정해줄 수 있다.
    static var empty: WeatherResult {
        return WeatherResult(main: Weather(temp: 0.0, humidity: 0.0))
    }
}

struct Weather: Codable {
    let temp: Double
    let humidity: Double
}

//  URLRequest+Extensions.swift
struct Resource<T> {
    let url: URL
}

extension URLRequest {
    static func load<T: Decodable>(resource: Resource<T>) -> Observable<T> {
        
        return Observable.from([resource.url])
            .flatMap { url -> Observable<Data> in
                let request = URLRequest(url: url)
                return URLSession.shared.rx.data(request: request)
            }.map { data -> T in // data throws in // 이라고 적을 수 있다 
                return try JSONDecoder().decode(T.self, from: data)
            }
    }
}

//  ViewController.swift
let resource = Resource<WeatherResult>(url: url)

let search = URLRequest.load(resource: resource)
    .observe(on: MainScheduler.instance)
    .asDriver(onErrorJustReturn: WeatherResult.empty)

search.map { "\($0.main.temp) 🌡" }
    .drive(self.temperatureLabel.rx.text)
    .disposed(by: disposeBag)
```

## 이렇게는 하지마

괜찮아 보여도 이렇게는 하지마

```swift
class NetworkManager {
	func load<T>(url: URL, withCompletion completion: @escaping (T?) -> Void) {
		let task = URLSession.shared.dataTask(with: url) { (data, _, _,) -> Void in
			guard let data = data else {
				DispatchQueue.main.async { completion(nil) }
				return
			}
			switch T.self {
			case is UIImage.Type:
				DispatchQueue.main.async { completion(UIImage(data: data) as? T) }
			case is Question.Type:
				let wrapper = try? JSONDecoder().decode(Wrapper<Question>.self, from: data)
				DispatchQueue.main.async { completion(wrapper?.items[0] as? T) }
			case is [Question].Type:
				let wrapper = try? JSONDecoder().decode(Wrapper<Question>.self, from: data)
				DispatchQueue.main.async { completion(wrapper?.items as? T) }
			default: break
			}
		}
		task.resume()
	}
}
```

이렇게 작게 쪼개기

```swift
class NetworkManager {
	func load(url: URL, withCompletion completion: @escaping (Data?) -> Void) {
		let task = URLSession.shared.dataTask(with: url) { (data, _, _,) -> Void in
			DispatchQueue.main.async { completion(data) }
		}
		task.resume()
	}
	
	func loadImage(with url: URL, completion: @escaping (UIImage?) -> Void) {
		load(url: url) { data in
			if let data = data {
				completion(UIImage(data: data))
			} else {
				completion(nil)
			}
		}
	}
	
	func loadTopQuestions(completion: @escaping ([Question]?) -> Void) {
		let url = URL(string: "https://api.stackexchange.com/2.2/questions?order=desc&sort=votes&site=stackoverflow")!
		load(url: url) { data in
			guard let data = data else {
				completion(nil)
				return
			}
			let wrapper = try? JSONDecoder().decode(Wrapper<Question>.self, from: data)
			completion(wrapper?.items)
		}
	}
}
```

## 이렇게 코딩할 수 있으면 좋겠다 

```swift
//
//  NetworkRequest.swift
//  TopQuestion
//
//  Created by Matteo Manferdini on 12/09/2019.
//  Copyright © 2019 Matteo Manferdini. All rights reserved.
//

import Foundation
import UIKit

protocol NetworkRequest: AnyObject {
	associatedtype ModelType
	func decode(_ data: Data) -> ModelType?
	func load(withCompletion completion: @escaping (ModelType?) -> Void)
}

extension NetworkRequest {
	fileprivate func load(_ url: URL, withCompletion completion: @escaping (ModelType?) -> Void) {
		let session = URLSession(configuration: .default, delegate: nil, delegateQueue: .main)
		let task = session.dataTask(with: url, completionHandler: { [weak self] (data: Data?, response: URLResponse?, error: Error?) -> Void in
			guard let data = data else {
				completion(nil)
				return
			}
			completion(self?.decode(data))
		})
		task.resume()
	}
}

class ImageRequest {
	let url: URL
	
	init(url: URL) {
		self.url = url
	}
}

extension ImageRequest: NetworkRequest {
	func decode(_ data: Data) -> UIImage? {
		return UIImage(data: data)
	}
	
	func load(withCompletion completion: @escaping (UIImage?) -> Void) {
		load(url, withCompletion: completion)
	}
}

class APIRequest<Resource: APIResource> {
	let resource: Resource
	
	init(resource: Resource) {
		self.resource = resource
	}
}

extension APIRequest: NetworkRequest {
	func decode(_ data: Data) -> [Resource.ModelType]? {
		let wrapper = try? JSONDecoder().decode(Wrapper<Resource.ModelType>.self, from: data)
		return wrapper?.items
	}
	
	func load(withCompletion completion: @escaping ([Resource.ModelType]?) -> Void) {
		load(resource.url, withCompletion: completion)
	}
}

protocol APIResource {
	associatedtype ModelType: Decodable
	var methodPath: String { get }
}

extension APIResource {
	var url: URL {
		var components = URLComponents(string: "https://api.stackexchange.com/2.2")!
		components.path = methodPath
		components.queryItems = [
			URLQueryItem(name: "site", value: "stackoverflow"),
			URLQueryItem(name: "order", value: "desc"),
			URLQueryItem(name: "sort", value: "votes"),
			URLQueryItem(name: "tagged", value: "ios")
		]
		return components.url!
	}
}

struct QuestionsResource: APIResource {
	typealias ModelType = Question
	let methodPath = "/questions"
}

```

## References

[Network Requests and REST APIs in iOS with Swift (Protocol-Oriented Approach)](https://matteomanferdini.com/network-requests-rest-apis-ios-swift/)