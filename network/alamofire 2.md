# [HowTo] Alamofire + JSONDecoder Example Code

### JSON

```JSON
{
  "coord": {
    "lon": 126.9778,
    "lat": 37.5683
  },
  "weather": [
    {
      "id": 600,
      "main": "Snow",
      "description": "light snow",
      "icon": "13n"
    }
  ],
  "base": "stations",
  "main": {
    "temp": 1.67,
    "feels_like": -3.95,
    "temp_min": 1,
    "temp_max": 2,
    "pressure": 1013,
    "humidity": 87
  },
  "visibility": 10000,
  "wind": {
    "speed": 5.14,
    "deg": 10
  },
  "snow": {
    "1h": 0.36
  },
  "clouds": {
    "all": 90
  },
  "dt": 1614594838,
  "sys": {
    "type": 1,
    "id": 8105,
    "country": "KR",
    "sunrise": 1614549846,
    "sunset": 1614590713
  },
  "timezone": 32400,
  "id": 1835848,
  "name": "Seoul",
  "cod": 200
}
```

### Model

```swift
//Model

struct WeatherData: Decodable {
    let name: String
    let main: Main
    let weather: [Weather]
    
    var model: WeatherModel {
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

### Manager

```swift
//WeatherManager
struct WeatherManager {
    
    private let API_KEY: String = //
    
    func fetchWeahter(byCity city: String, completion: @escaping (Result<WeatherData, Error>) -> Void) {
        
        let query = city.addingPercentEncoding(withAllowedCharacters: .urlHostAllowed) ?? city
        
        let path = "https://api.openweathermap.org/data/2.5/weather?q=\(query)&APPID=\(API_KEY)&units=metric"
        let urlString = String(format: path)
        
        AF.request(urlString).responseDecodable(of: WeatherData.self, queue: .main, decoder: JSONDecoder()) { (response) in
            
            switch response.result {
            
            case .success(let weatherData):
                completion(.success(weatherData))
            case .failure(let error):
                completion(.failure(error))
            }
        }
    }
}
```

### ViewController

```swift
//viewcontroller

//MARK: - Properties
private let weatherManager = WeatherManager()

//MARK: - Helpers
private func fetchWeather() {
    weatherManager.fetchWeahter(byCity: "Seoul") {[weak self] (result) in
        guard let self = self else { return }
        switch result {
        case .success(let weatherData):
            self.updateView(with: weatherData)
        case .failure(let error):
            print(error.localizedDescription)
        }
    }
}
```

## References

[Alamofire Description](https://github.com/Alamofire/Alamofire/blob/master/Documentation/Usage.md)

[Alamofire Project Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/AFWeather)