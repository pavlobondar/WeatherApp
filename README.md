# WeatherApp

A native application built over a day that returns data from [Weatherstack](https://weatherstack.com) and parses the JSON into viewable results. 

## Getting Started

The first step to using the API is to authenticate with your weatherstack account's unique API access key, which can be found in your account dashboard after registration. To authenticate with the API, simply use the base URL below and pass your API access key to the API's access_key parameter. ([Details](https://weatherstack.com/documentation))

```
class NetworkService {
    private let ACCESS_KEY = "YOUR_ACCESS_KEY"
        func fetchWeather(search: String?, completionHandler: @escaping (CurrentWeather?, Error?) -> ()) {
        var components = URLComponents()
        components.scheme = "http"
        components.host = "api.weatherstack.com"
        components.path = "/current"
        components.queryItems = [URLQueryItem(name: "access_key", value: ACCESS_KEY),
                                 URLQueryItem(name: "query", value: search)]
        guard let url = components.url else { return }
        URLSession
            .shared
            .dataTask(with: url) { (data, response, error) in
                guard let response = response as? HTTPURLResponse else { return }
                if let data = data, (200...299).contains(response.statusCode) {
                    do {
                        let weatherData = try JSONDecoder().decode(CurrentWeather.self, from: data)
                        completionHandler(weatherData, nil)
                    } catch {
                        print(error.localizedDescription)
                        completionHandler(nil, error)
                    }
                }
        }.resume()
    }
}
```