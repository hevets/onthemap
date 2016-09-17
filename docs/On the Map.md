# On the Map

Project Description:

- post and retrieve data using apples networking framework
- allow students to authenticate a user
- creating user interfaces that are responsive, and communicate network activity
- use core location and mapkit framework to display pins on a map







### <u>Rough Outline</u>

- User management

  - email + password (2 input fields)
    - possibly add sign up link
  - signup to udacity (this link is text)
  - button to login
    - if login fails it will alert the user with an alert view to let them know there was a problem logging into the app

- Tabbar with two views

  - MapView
  - ListView

- BothViews

  - Share the same data source ?
    - method to download the latest 100 most recent locations
  - Shares a common Networking layer that can access the udacity API

- MapView

  - takes datasource and creates MKPointAnnotations from this shared datasource
  - shows MKPointAnnotation objects on the map where users have posted data
  - Can add a pin to the map
    - Will prevent a user from adding more than 1 pin to the map
      - alert will show prompting the user to overwrite or cancel
  - Can tap on a pin to view its content
    - name as the big text
    - url link as the detail text
  - Can tap on refresh to fetch from Udacity

- ListView

  - when i tap on an item in the list view it will take me to the students url

- Add location view

  - Asks where are you studying today

    - Enter a place

      - This gets reverse geocoded
      - it then presents a modal 50/50 with ability to add a link and submit the location
      - on submit the modal is dismissed and you are brought back to the mapview

      ​

### <u>**Parse**</u>

**Documentation:** https://parseplatform.github.io//docs/rest/guide/#objects

**Server URL:** https://parse.udacity.com/parse/classes

**Parse Application ID:** QrX47CA9cyuGewLdsL7o5Eb8iug6Em8ye0dnAbIr

**Rest API Key:** QuWThTdiRmTux3YaDseUSEpUKo7aBYM737yKd4gY

**StudentLocation** objects will be what we're working with for the parse data



### <u>Playground Setup</u>

The following snippet will allow the playground code to execute indefinitely 

```swift
//: Playground - noun: a place where people can play

import UIKit
import XCPlayground

// this line tells the Playground to execute indefinitely
XCPlaygroundPage.currentPage.needsIndefiniteExecution = true

let urlString = "http://quotes.rest/qod.json?category=inspire"
let url = NSURL(string: urlString)
let request = NSMutableURLRequest(URL: url!)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
    if error != nil { // Handle error
        return
    }
    print(NSString(data: data!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



### <u>Facebook Login</u> (optional)

- Read through facebook login feature documentation [Docs](https://developers.facebook.com/docs/ios/getting-started), [Facebook Preparing iOS 9](https://developers.facebook.com/docs/ios/ios9), 
- Facebook App ID: `365362206864879`
- Facebook URL Scheme Suffix: `onthemap`
- Connect your Udacity user account to your facebook account
- Implement a few extra methods (Udacity API Guide)
- To delete a session refer below to Logging out with Facebook



##### Create a Session

- `POST https://www.udacity.com/api/session`
- `FB App ID: 365362206864879`
- URL scheme suffix `onthemap`
- Required params `*facebook_mobile*, *access_token*

```swift
// Example POSTing a Session with Facebook
let request = NSMutableURLRequest(URL: NSURL(string: "https://www.udacity.com/api/session")!)
request.HTTPMethod = "POST"
request.addValue("application/json", forHTTPHeaderField: "Accept")
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.HTTPBody = "{\"facebook_mobile\": {\"access_token\": \"DADFMS4SN9e8BAD6vMs6yWuEcrJlMZChFB0ZB0PCLZBY8FPFYxIPy1WOr402QurYWm7hj1ZCoeoXhAk2tekZBIddkYLAtwQ7PuTPGSERwH1DfZC5XSef3TQy1pyuAPBp5JJ364uFuGw6EDaxPZBIZBLg192U8vL7mZAzYUSJsZA8NxcqQgZCKdK4ZBA2l2ZA6Y1ZBWHifSM0slybL9xJm3ZBbTXSBZCMItjnZBH25irLhIvbxj01QmlKKP3iOnl8Ey;\"}}".dataUsingEncoding(NSUTF8StringEncoding)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error...
      return
  }
  let newData = data.subdataWithRange(NSMakeRange(5, data.length - 5)) /* subset response data! */
  print(NSString(data: newData!, encoding: NSUTF8StringEncoding))
}
task.resume()
```





### <u>Student Location</u>

Access the student location schema and spec [here](https://classroom.udacity.com/nanodegrees/nd003/parts/00313454010/modules/307169911375462/lessons/3071699113239847/concepts/461d05e1-a949-42b2-bb57-c6b861a2e524) 

- parse types can be cast into swift types



##### Fetch Multiple Students

- `GET https://parse.udacity.com/parse/classes/StudentLocation`


- `limit=100`, `skip=0`, `order=updatedAt`

```swift
// Example request to fetch StudentLocation
let request = NSMutableURLRequest(URL: NSURL(string: "https://parse.udacity.com/parse/classes/StudentLocation")!)
request.addValue("QrX47CA9cyuGewLdsL7o5Eb8iug6Em8ye0dnAbIr", forHTTPHeaderField: "X-Parse-Application-Id")
request.addValue("QuWThTdiRmTux3YaDseUSEpUKo7aBYM737yKd4gY", forHTTPHeaderField: "X-Parse-REST-API-Key")
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error...
      return
  }
  print(NSString(data: data!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



**Fetch a Single StudentLocation**

- `GET https://parse.udacity.com/parse/classes/StudentLocation`

- `where={"uniqueKey"="1234"}`

  ```swift
  // Example request
  let urlString = "https://parse.udacity.com/parse/classes/StudentLocation?where=%7B%22uniqueKey%22%3A%221234%22%7D"
  let url = NSURL(string: urlString)
  let request = NSMutableURLRequest(URL: url!)
  request.addValue("QrX47CA9cyuGewLdsL7o5Eb8iug6Em8ye0dnAbIr", forHTTPHeaderField: "X-Parse-Application-Id")
  request.addValue("QuWThTdiRmTux3YaDseUSEpUKo7aBYM737yKd4gY", forHTTPHeaderField: "X-Parse-REST-API-Key")
  let session = NSURLSession.sharedSession()
  let task = session.dataTaskWithRequest(request) { data, response, error in
    if error != nil { // Handle error
        return
    }
    print(NSString(data: data!, encoding: NSUTF8StringEncoding))
  }
  task.resume()
  ```



**Post a StudentLocation**

- `POST https://parse.udacity.com/parse/classes/StudentLocation`

```swift
// Example of POST student
let request = NSMutableURLRequest(URL: NSURL(string: "https://parse.udacity.com/parse/classes/StudentLocation")!)
request.HTTPMethod = "POST"
request.addValue("QrX47CA9cyuGewLdsL7o5Eb8iug6Em8ye0dnAbIr", forHTTPHeaderField: "X-Parse-Application-Id")
request.addValue("QuWThTdiRmTux3YaDseUSEpUKo7aBYM737yKd4gY", forHTTPHeaderField: "X-Parse-REST-API-Key")
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.HTTPBody = "{\"uniqueKey\": \"1234\", \"firstName\": \"John\", \"lastName\": \"Doe\",\"mapString\": \"Mountain View, CA\", \"mediaURL\": \"https://udacity.com\",\"latitude\": 37.386052, \"longitude\": -122.083851}".dataUsingEncoding(NSUTF8StringEncoding)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error…
      return
  }
  print(NSString(data: data!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



**Put a StudentLocation**

- `PUT https://parse.udacity.com/parse/classes/StudentLocation/<objectId>`
- Required params "*objectId*"

```swift
let urlString = "https://parse.udacity.com/parse/classes/StudentLocation/8ZExGR5uX8"
let url = NSURL(string: urlString)
let request = NSMutableURLRequest(URL: url!)
request.HTTPMethod = "PUT"
request.addValue("QrX47CA9cyuGewLdsL7o5Eb8iug6Em8ye0dnAbIr", forHTTPHeaderField: "X-Parse-Application-Id")
request.addValue("QuWThTdiRmTux3YaDseUSEpUKo7aBYM737yKd4gY", forHTTPHeaderField: "X-Parse-REST-API-Key")
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.HTTPBody = "{\"uniqueKey\": \"1234\", \"firstName\": \"John\", \"lastName\": \"Doe\",\"mapString\": \"Cupertino, CA\", \"mediaURL\": \"https://udacity.com\",\"latitude\": 37.322998, \"longitude\": -122.032182}".dataUsingEncoding(NSUTF8StringEncoding)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error…
      return
  }
  print(NSString(data: data!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



### <u>Udacity API</u>



##### Create a Session

- Skip the first 5 characters for all responses on the Udacity API, these are used for security purposes.
- To authenticate with udacity we need a session
  - `POST https://www.udacity.com/api/session`
  - Content-Type is set to application/json
  - `udacity: { username: 'hevets', password: 'test'}` (encode this using UTF8Encoding)

```swift
// Example of getting a udacity session
let request = NSMutableURLRequest(URL: NSURL(string: "https://www.udacity.com/api/session")!)
request.HTTPMethod = "POST"
request.addValue("application/json", forHTTPHeaderField: "Accept")
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.HTTPBody = "{\"udacity\": {\"username\": \"account@domain.com\", \"password\": \"********\"}}".dataUsingEncoding(NSUTF8StringEncoding)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error…
      return
  }
  let newData = data.subdataWithRange(NSMakeRange(5, data.length - 5)) /* subset response data! */
  print(NSString(data: newData!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



##### Delete a Session

- `DELETE https://www.udacity.com/api/session`

```swift
let request = NSMutableURLRequest(URL: NSURL(string: "https://www.udacity.com/api/session")!)
request.HTTPMethod = "DELETE"
var xsrfCookie: NSHTTPCookie? = nil
let sharedCookieStorage = NSHTTPCookieStorage.sharedHTTPCookieStorage()
for cookie in sharedCookieStorage.cookies! {
  if cookie.name == "XSRF-TOKEN" { xsrfCookie = cookie }
}
if let xsrfCookie = xsrfCookie {
  request.setValue(xsrfCookie.value!, forHTTPHeaderField: "X-XSRF-TOKEN")
}
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error…
      return
  }
  let newData = data.subdataWithRange(NSMakeRange(5, data.length - 5)) /* subset response data! */
  print(NSString(data: newData!, encoding: NSUTF8StringEncoding))
}
task.resume()
```



##### Getting user data

- `GET https://www.udacity.com/api/users/<user_id>`

```swift
// Get user data from Udacity API
let request = NSMutableURLRequest(URL: NSURL(string: "https://www.udacity.com/api/users/3903878747")!)
let session = NSURLSession.sharedSession()
let task = session.dataTaskWithRequest(request) { data, response, error in
  if error != nil { // Handle error...    
      return
  }
  let newData = data.subdataWithRange(NSMakeRange(5, data.length - 5)) /* subset response data! */
  print(NSString(data: newData!, encoding: NSUTF8StringEncoding))
}
task.resume()
```

