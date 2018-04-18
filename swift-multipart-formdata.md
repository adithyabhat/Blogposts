title: Swift 4 and iOS: Posting multipart form data using URLSession
tags: swift,ios,ios-urlrequest

For posting multi part form data in Swift iOS, you need to have the data formatted in a specific format. The content has to contain a boundry constant, at the start and end. 

Below is the Multipart form data helper class, that contains a method that makes it convinient to create the header and body data that needs to be passed when posting the data.

```
class MultipartFormDataHelper: NSObject {
    static func headerAndBodyData(withParameters parameters: [String: String]?,
                                  keyPath: String,
                                  data: Data?,
                                  mimeType: String?,
                                  fileName: String?) -> ([String: String], Data) {
        var bodyData = Data()
        let boundry = boundryString()
        let header = ["Content-Type": "multipart/form-data; boundary=\(boundry)"]

        if let parameters = parameters {
            for (key, value) in parameters {
                bodyData.append("--\(boundry)\r\n".utf8EncodedData()!)
                bodyData.append("Content-Disposition: form-data; name=\"\(key)\"\r\n\r\n".utf8EncodedData()!)
                bodyData.append("\(value)\r\n".utf8EncodedData()!)
            }
        }
        
        if data != nil, fileName != nil, mimeType != nil {
            bodyData.append("--\(boundry)\r\n".utf8EncodedData()!)
            bodyData.append("Content-Disposition: form-data; name=\"\(keyPath)\"; filename=\"\(fileName!)\"\r\n".utf8EncodedData()!)
            bodyData.append("Content-Type: \(mimeType!)\r\n\r\n".data(using: .utf8)!)
            bodyData.append(data!)
            bodyData.append("\r\n".utf8EncodedData()!)
            bodyData.append("--\(boundry)--\r\n".utf8EncodedData()!)
        }
        
        return (header, bodyData)
    }
    
    private static func boundryString() -> String {
        return UUID().uuidString
    }
}

extension String {
    func utf8EncodedData() -> Data? {
        return self.data(using: .utf8)
    }
}
```

In my example below, I am just posting image as form data. The server expects image data in the "files" keypath.

```
let imageData = UIImagePNGRepresentation(image)

var (header, bodyData) = MultipartFormDataHelper.headerAndBodyData(withParameters: nil,
                                                                           keyPath: "files",
                                                                           data: imageData,
                                                                           mimeType: "png",
                                                                           fileName: "image.png")
 ```
 
 After the method executes, the tuple returned contains the header and bodyData. The header data can be further mutated by adding data and set to `allHTTPHeaderFields` property and bodyData is set to `httpData` property of the URLRequest                                                                           
                                                 
