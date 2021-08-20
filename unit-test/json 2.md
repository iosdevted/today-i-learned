# Basics of Unit Testing of JSON

```json
{
   "data":[
      {
         "wind_cdir":"NE",
         "rh":59,
         "pod":"d",
         "lon":"-78.63861",
         "pres":1006.6,
         "timezone":"America\/New_York",
         "ob_time":"2017-08-28 16:45",
         "country_code":"US",
         "clouds":75,
         "vis":10,
         "wind_spd":6.17,
         "wind_cdir_full":"northeast",
         "app_temp":24.25,
         "state_code":"NC",
         "ts":1503936000,
         "h_angle":0,
         "dewpt":15.65,
         "weather":{
            "icon":"c03d",
            "code":"803",
            "description":"Broken clouds"
         },
         "uv":2,
         "aqi":45,
         "station":"CMVN7",
         "wind_dir":50,
         "elev_angle":63,
         "datetime":"2017-08-28:17",
         "precip":0,
         "ghi":444.4,
         "dni":500,
         "dhi":120,
         "solar_rad":350,
         "city_name":"Raleigh",
         "sunrise":"10:44",
         "sunset":"23:47",
         "temp":24.19,
         "lat":"35.7721",
         "slp":1022.2
      }
   ],
   "count":1
}
```

```swift
class WeatherbitDataTests: XCTestCase {
  private static let dateFormatter: DateFormatter = {
    let formatter = DateFormatter()
    formatter.dateFormat = "MM-dd-yyyy"
    return formatter
  }()
  
  var exampleJSONData: Data!
  var weather: WeatherbitData!

  override func setUp() {
    let bundle = Bundle(for: type(of: self))
    let url = bundle.url(forResource: "WeatherbitExample", withExtension: "json")!
    exampleJSONData = try! Data(contentsOf: url)
  
    let decoder = JSONDecoder()
    weather = try! decoder.decode(WeatherbitData.self, from: exampleJSONData)
  }
    
  func testDecodeTemp() throws {
    XCTAssertEqual(weather.currentTemp, 24.19)
  }
  
  func testDecodeIcon() throws {
    XCTAssertEqual(weather.iconName, "c03d")
  }
  
  func testDecodeDescription() throws {
    XCTAssertEqual(weather.description, "Broken clouds")
  }
  
  func testDecodeDate() throws {
    XCTAssertEqual(Self.dateFormatter.string(from: weather.date), "08-28-2017")
  }

}

```