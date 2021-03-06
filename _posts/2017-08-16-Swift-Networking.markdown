---
layout:     post
title:      "Swift Networking"
subtitle:   "URLSession, JSONParse, manipulating data"
date:       2017-08-16 10:55:55 AM
author:     "Rob"
---

# Getting Started

I signed back up with udacity.com to earn my iOS Nanodegree. It's going to prove a bit challenging, but I'm almost halfway through it. The current lesson that I'm working on is the Networking section of the Nanodegree. Here there are several videos as well as a final project in the course. Network requests are the third lesson in the iOS Nanodegree program. 

I'm really excited to get this completed, and I think that my notes here will help both you and I in the end. There are some really important topics covered in this section of the nanodegree; working with URLSession, URLSessionWithDataTask, JSONSerialization (Converting and parsing JSON data), and more. 

In this guide, we will go through the various aspects of SleepingInTheLibrary. Yes you want to be about speed, but you also do want to remember the concepts. You will learn the following: 

- How to use Flickrs web service to make an API call for data.
- How to manipulate the data within.
- Swift dictionaries and how they work. 
- Using a helper function like URLComponents
- Storing data in Key-Value Pairs. 
- Error handling in case something goes wrong. 

By the time you're done this blog post, you should have a pretty good understanding of API calls, and some good notion of how and why you would use a dictionary. 

# Error Handling

Swift can handle errors very well. We use error handling to tell our programs what to do if there were an error encountered. Say you want to keep retrying a connection if there was an error found. 

You can perform Error handling as such: 

``` swift
do { 

} catch { 
displayError("Could not grab \(data)")
}

```

# Glossary

Ultimately, I use this website as a reference and I like to hold on to important vocabulary. I believe that vocabulary is just as important as software development itself. 

Here are some key concepts to study and understand: 

- **Web service** — a software application that enables the communication of data over a network.

- **API (Application Program Interface)** — a set of methods that are specialized for a purpose; many web services are also referred to as APIs or “network” APIs.

- **JSON (JavaScript Object Notation)** — a data format used frequently to communicate data over a network.

- **Parameters (in a URL)** — a value specified in a URL usually in the format of “parameter=value”.

- **ASCII (American Standard Code for Information Interchange)** — a simple way of representing characters in a computer.

- **URL escaping/encoding** — a mechanism for ensuring that all characters in a URL are valid ASCII characters.

- **API key** — a value distributed by an API provider that uniquely identifies applications using it service.

- **URLRequest** — represents a configurable URL that can specify properties such as the HTTP method type.

- **Serialization** — converting an object into a stream of bytes; this is often done to send an object over the network so that it can be recreated somewhere else.

- **Deserialization** — converting a stream of bytes into an object; the opposite of serialization.

- **JSONSerialization** — a class used to convert bytes of JSON data into objects or vice versa.

- **NSDictionary** — Objective-C’s version of a Dictionary; sometimes we must use it in Swift when working with Objective-C APIs.

- **NSArray — Objective-C’s version of an Array; sometimes we must use it in Swift when working with Objective-C APIs.


# Setup 

Begin setting up our data by writing the following code in the example below. I will be using a very simple API web service provided by Sandip Bhagat.

[Sandipbgt The Astrologers API](http://sandipbgt.com/theastrologer/)

--- 

# Creating URLSession
In the code example below, I want to go through each part of the breakdown. 


``` swift 
// Instantiate the URLSession Singleton class, create a url and request
let session = URLSession.shared
let urlString = URL("http://sandipbgt.com/theastrologer/api/horoscope/leo/today")
let url = URL(string: urlString)! 
let request = URLRequest(url: url) 
```

We begin by creating an instance of the URLSession.shared singleton. We then create the actual urlString and define it with URL("http://"). We store this inside of the url constant and forcibly unwrap the optional as we know that there is a value stored inside of it. We create a new constant named request and use our newly created url. 

# Creating an error function. 

``` swift
let task = session.dataTask(with: request) { (data, response, error) in     
    func displayError(_ error: String) {
        print(error)
        print("URL at time of error: \(url)")
        performUIUpdatesOnMain { 
            self.setUIEnabled(true) 
        }
    }
}

```
# URL to Components

> When we are constructing our URL it is important that we use URLComponents. 

Note the methods have been updated in Swift 4: 

- NSURLComponents has been renamed to URLComponents.

- NSURLQueryItem has been renamed to URLQueryItem.

Breaking apart your request and each part into their own Components. Below is an example of how this is accomplished in Swift. 

``` swift
import Foundation

// Create an instance of URLComponents
var components = URLComponents()

// Based on RFC 
components.scheme = "https"
components.host = "api.flickr.com"
components.path = "/services/rest"
components.queryItems = [URLQueryItem]() 

let queryItem1 = URLQueryItem(name: "method", value: "flickr.photos.search")
let queryItem2 = URLQueryItem(name: "api_key", value: "1234")
let queryItem3 = URLQueryItem(name: "text", value: "purple")

components.queryItems!.append(queryItem1)
components.queryItems!.append(queryItem2)
components.queryItems!.append(queryItem3)

print(components.url!) 

```

Right now, you're looking at a high level overview of the components structure and how iOS handles URL (or more specifically URIs). However, there is a much better way to understand this with a simple diagram. 

``` javascript
   The following are two example URIs and their component parts:

         foo://example.com:8042/over/there?name=ferret#nose
         \_/   \______________/\_________/ \_________/ \__/
          |           |            |            |        |
       scheme     host       path        query   fragment
          |   _____________________|__
         / \ /                        \
         urn:example:animal:ferret:nose

```

The above illustration is taken from RFC-3986 which details how URI and URLs are broken up in components. 

Basic terminology of terms: 

URN — Unnform Resource Name, the Scheme used, which can be `wss://`, `http://`, `ftp://` etc.
Query - The query can be identified with the ? symbol. 

# Updating UI On the Main Thread 

``` swift
if let imageData = try? Data(contentsOf: imageURL!) {
    performUIUpdatesOnMain {
        self.setUIEnabled(true)
        
        // cast the object into a UIImage and set it as the image for the imageView
        self.photoImageView.image = UIImage(data: imageData) 
        
        // set text from data 
        self.photoTitleLabel.text = photoTitle ?? "(Untitled)"
    }
}
```

When handling these kinds of operations in your application, it is extremely important that you understand the concept of multi-threaded programming, or in the case of Swift, Asynchronus tasks and Grand Central Dispatch. 

## Main thread

## Background Thread

Why do we want to do this? Well, the simplest explanation is that when you're pulling data from the web, your View will not automatically update on the main thread without first calling this method. Later on, we'll talk more about how asynchronous tasks are handled, and how GCD and updating views on the main thread will make your app work very smoothly. 

# Breaking Down The Code

Here is the ViewController.swift file and we're going to tackle each part of it in chunks. Breaking down the app into parts will make it a lot easier to understand whats going on.



``` swift
//
//  ViewController.swift
//  FlickFinder 
//
//  Created by Rob McElvenny on 11/5/15.
//  Copyright © 2015 Robert McElvenny. All rights reserved.
//

``` 

Initial code comments, you can add any licenses here, or additional details about your code here. 


``` swift
import UIKit

// MARK: - ViewController: UIViewController

class ViewController: UIViewController {
    
    // MARK: Properties
    
    var keyboardOnScreen = false
    
    // MARK: Outlets
    
    @IBOutlet weak var photoImageView: UIImageView!
    @IBOutlet weak var photoTitleLabel: UILabel!
    @IBOutlet weak var phraseTextField: UITextField!
    @IBOutlet weak var phraseSearchButton: UIButton!
    @IBOutlet weak var latitudeTextField: UITextField!
    @IBOutlet weak var longitudeTextField: UITextField!
    @IBOutlet weak var latLonSearchButton: UIButton!
    
``` 

The ``` swift // Mark:``` and ```// Mark: - ``` syntax in Swift have the same behavior identical to the `#pragma mark` and `#pragma mark -` syntax in Objective-C. This allows you to get extra information to show up in the quick jump bar. This works the exact same for ` // TODO:` and ` // FIXME: ` (Extra information that shows up inside of the quick jump bar.)

``` swift 
    // MARK: Life Cycle
    override func viewDidLoad() {
        super.viewDidLoad()
        phraseTextField.delegate = self
        latitudeTextField.delegate = self
        longitudeTextField.delegate = self
        subscribeToNotification(.UIKeyboardWillShow, selector: #selector(keyboardWillShow))
        subscribeToNotification(.UIKeyboardWillHide, selector: #selector(keyboardWillHide))
        subscribeToNotification(.UIKeyboardDidShow, selector: #selector(keyboardDidShow))
        subscribeToNotification(.UIKeyboardDidHide, selector: #selector(keyboardDidHide))
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        unsubscribeFromAllNotifications()
    }
    
    
```
In this snippet of code, we set the delegates on our 3 text fields { 
...
} I'll fill more details about this later. The subscribeToNotification calls are known as observers, this allows your application to "listen" to events, which is ultimately necessary in shifting your view upwards when the textField is selected. In `viewWillDisappear` we unsubscribe our observers from the notifications. 

``` swift
    
    // MARK: Search Actions
    
    @IBAction func searchByPhrase(_ sender: AnyObject) {

        userDidTapView(self)
        setUIEnabled(false)
        
        if !phraseTextField.text!.isEmpty {
            photoTitleLabel.text = "Searching..."
            let methodParameters = [
                Constants.FlickrParameterKeys.Method: Constants.FlickrParameterValues.SearchMethod,
                Constants.FlickrParameterKeys.APIKey: Constants.FlickrParameterValues.APIKey,
                Constants.FlickrParameterKeys.Text: phraseTextField.text!,
                Constants.FlickrParameterKeys.SafeSearch: Constants.FlickrParameterValues.UseSafeSearch,
                Constants.FlickrParameterKeys.Extras: Constants.FlickrParameterValues.MediumURL,
                Constants.FlickrParameterKeys.Format: Constants.FlickrParameterValues.ResponseFormat,
                Constants.FlickrParameterKeys.NoJSONCallback: Constants.FlickrParameterValues.DisableJSONCallback
            ]
            displayImageFromFlickrBySearch(methodParameters as [String:AnyObject])
        } else {
            setUIEnabled(true)
            photoTitleLabel.text = "Phrase Empty."
        }
    }
    
```
We create a function to search by the phrase that is currently inside of our textField, we use AnyObject meaning that AnyObject type can be used as the sender; We set `userDidTapView(self)` which is a gesture recognizer that we have created and detects if the user tapped anywhere on the view (This will cause the keyboard to disappear). 

in our if statement, we check if the phraseTextField.text is not empty, if it is not empty set the label text to "Searching..." and create our methodParameter constant, stored with dictionary Key-Value pairs found in our Constants.swift file. 

(Add Constants.swift data)

We then call displayImageFromFlickrBySearch(methodParameters) and cast it as a dictionary of [String : AnyObject]

If the phraseTextField.text.isEmpty is Actually empty, it will retext the label to "Phrase Empty."

``` swift
    
    @IBAction func searchByLatLon(_ sender: AnyObject) {

        userDidTapView(self)
        setUIEnabled(false)
        
        if isTextFieldValid(latitudeTextField, forRange: Constants.Flickr.SearchLatRange) && isTextFieldValid(longitudeTextField, forRange: Constants.Flickr.SearchLonRange) {
            photoTitleLabel.text = "Searching..."
            let methodParameters = [
                Constants.FlickrParameterKeys.Method: Constants.FlickrParameterValues.SearchMethod,
                Constants.FlickrParameterKeys.APIKey: Constants.FlickrParameterValues.APIKey,
                Constants.FlickrParameterKeys.BoundingBox: bboxString(),
                Constants.FlickrParameterKeys.SafeSearch: Constants.FlickrParameterValues.UseSafeSearch,
                Constants.FlickrParameterKeys.Extras: Constants.FlickrParameterValues.MediumURL,
                Constants.FlickrParameterKeys.Format: Constants.FlickrParameterValues.ResponseFormat,
                Constants.FlickrParameterKeys.NoJSONCallback: Constants.FlickrParameterValues.DisableJSONCallback
            ]
            displayImageFromFlickrBySearch(methodParameters as [String:AnyObject])
        }
        else {
            setUIEnabled(true)
            photoTitleLabel.text = "Lat should be [-90, 90].\nLon should be [-180, 180]."
        }
    }
    
```

We create a new function that is similar, but instead we're using two different text fields. using the Constants.swift file as well, but we also use our BoundingBox : bboxString() <- which is a function that we have created as well. 

``` swift
    private func bboxString() -> String {        
        // ensure bbox is bounded by minimum and maximums
        if let latitude = Double(latitudeTextField.text!), let longitude = Double(longitudeTextField.text!) {
            let minimumLon = max(longitude - Constants.Flickr.SearchBBoxHalfWidth, Constants.Flickr.SearchLonRange.0)
            let minimumLat = max(latitude - Constants.Flickr.SearchBBoxHalfHeight, Constants.Flickr.SearchLatRange.0)
            let maximumLon = min(longitude + Constants.Flickr.SearchBBoxHalfWidth, Constants.Flickr.SearchLonRange.1)
            let maximumLat = min(latitude + Constants.Flickr.SearchBBoxHalfHeight, Constants.Flickr.SearchLatRange.1)
            return "\(minimumLon),\(minimumLat),\(maximumLon),\(maximumLat)"
        } else {
            return "0,0,0,0"
        }
    }
```
Our bboxString() -> String { ... } returns a string value and we set our text fields, and set our min values and max values. This is how we create a bounding box to search images within.The formula is min - half, max - half and search with the text found in the fields. (More clarifcation later one) 

``` swift
        
    // MARK: Flickr API
    
    private func displayImageFromFlickrBySearch(_ methodParameters: [String: AnyObject]) {
        
        // create session and request
        let session = URLSession.shared
        let request = URLRequest(url: flickrURLFromParameters(methodParameters))
        
        // create network request
        let task = session.dataTask(with: request) { (data, response, error) in
            
            // if an error occurs, print it and re-enable the UI
            func displayError(_ error: String) {
                print(error)
                performUIUpdatesOnMain {
                    self.setUIEnabled(true)
                    self.photoTitleLabel.text = "No photo returned. Try again."
                    self.photoImageView.image = nil
                }
            }

``` 

Here we create our function which will search for the image by phrase. We go about creating our network session and request, and then run the network request with the data task using the request constant, we use a completion handler to handle each part of data, response, error. 

We use a custom error display to show errors and update the UI on the main thread. 


``` swift
            /* GUARD: Was there an error? */
            guard (error == nil) else {
                displayError("There was an error with your request: \(error)")
                return
            }
```

## Guard 



We handle each error, 

``` swift
            
            /* GUARD: Did we get a successful 2XX response? */
            guard let statusCode = (response as? HTTPURLResponse)?.statusCode, statusCode >= 200 && statusCode <= 299 else {
                displayError("Your request returned a status code other than 2xx!")
                return
            }
```

``` swift
            /* GUARD: Was there any data returned? */
            guard let data = data else {
                displayError("No data was returned by the request!")
                return
            }
```

Check if there is no data, if there isn't display an error and return. 

``` swift
            // parse the data
            let parsedResult: [String:AnyObject]!
            do {
                parsedResult = try JSONSerialization.jsonObject(with: data, options: .allowFragments) as! [String:AnyObject]
            } catch {
                displayError("Could not parse the data as JSON: '\(data)'")
                return
            }
```

Here's where we parse the data, our parsed data will be a dictionary of String : AnyObject, we use ! to forcibly unwrap the values (Since we know that there will be data). we then serialize the data with the JSONSerialization.(jsonObjects) and pass our data to it, and cast it to a Dictionary of String key and AnyObject values. 

We then use the Do { } catch { } to display an errors and show where the error occurred in our data. 

``` swift
            /* GUARD: Did Flickr return an error (stat != ok)? */
            guard let stat = parsedResult[Constants.FlickrResponseKeys.Status] as? String, stat == Constants.FlickrResponseValues.OKStatus else {
                displayError("Flickr API returned an error. See error code and message in \(parsedResult)")
                return
            }
```

If there was an error within the parsedResult, we check the Status and cast it as a String. and we also compare it to the OKStatus, if it is not OKStatus then throw an error and print where the error is by \(parsedResult).

``` swift
            /* GUARD: Is "photos" key in our result? */
            guard let photosDictionary = parsedResult[Constants.FlickrResponseKeys.Photos] as? [String:AnyObject] else {
                displayError("Cannot find keys '\(Constants.FlickrResponseKeys.Photos)' in \(parsedResult)")
                return
            }
```
Literally the same error check, except we are checking the Photos key. 

``` swift
            /* GUARD: Is "pages" key in the photosDictionary? */
            guard let totalPages = photosDictionary[Constants.FlickrResponseKeys.Pages] as? Int else {
                displayError("Cannot find key '\(Constants.FlickrResponseKeys.Pages)' in \(photosDictionary)")
                return
            }
```

Same error except we're checking our newly created photosDictionary and the pages key, throw an error otherwise. 

``` swift
            // pick a random page!
            let pageLimit = min(totalPages, 40)
            let randomPage = Int(arc4random_uniform(UInt32(pageLimit))) + 1
            self.displayImageFromFlickrBySearch(methodParameters, withPageNumber: randomPage)
        }
        
        // start the task!
        task.resume()
    }
    
```

Setup our code to pick a random page, the minimum amount which is from totalPages, to 40

Use our Cast our arc4random value which automatically generates a value and adds 1, then case it as an Int since the arc4random_uniform returns as UInt32 value. 

from here, we display any image by calling the function with methodParameters and the pageNumber.

We also have our necessary task.resume() function 


Here's where I don't understand why we are making the same exact call to the same url, just with different methodParameters, instead of lat/long we are searching a phrase.

``` swift
    
    // FIX: For Swift 3, variable parameters are being depreciated. Instead, create a copy of the parameter inside the function.
    
    private func displayImageFromFlickrBySearch(_ methodParameters: [String: AnyObject], withPageNumber: Int) {
        
        // add the page to the method's parameters
        var methodParametersWithPageNumber = methodParameters
        methodParametersWithPageNumber[Constants.FlickrParameterKeys.Page] = withPageNumber as AnyObject?
        
        // create session and request
        let session = URLSession.shared
        let request = URLRequest(url: flickrURLFromParameters(methodParameters))
        
        // create network request
        let task = session.dataTask(with: request) { (data, response, error) in
            
            // if an error occurs, print it and re-enable the UI
            func displayError(_ error: String) {
                print(error)
                performUIUpdatesOnMain {
                    self.setUIEnabled(true)
                    self.photoTitleLabel.text = "No photo returned. Try again."
                    self.photoImageView.image = nil
                }
            }
```

From here we have already learned that it's all the same stuff, there is a better way of performing this. 

``` swift
            /* GUARD: Was there an error? */
            guard (error == nil) else {
                displayError("There was an error with your request: \(error)")
                return
            }
```   
            /* GUARD: Did we get a successful 2XX response? */
            guard let statusCode = (response as? HTTPURLResponse)?.statusCode, statusCode >= 200 && statusCode <= 299 else {
                displayError("Your request returned a status code other than 2xx!")
                return
            }
            
            /* GUARD: Was there any data returned? */
            guard let data = data else {
                displayError("No data was returned by the request!")
                return
            }
            
            // parse the data
            let parsedResult: [String:AnyObject]!
            do {
                parsedResult = try JSONSerialization.jsonObject(with: data, options: .allowFragments) as! [String:AnyObject]
            } catch {
                displayError("Could not parse the data as JSON: '\(data)'")
                return
            }
            
            /* GUARD: Did Flickr return an error (stat != ok)? */
            guard let stat = parsedResult[Constants.FlickrResponseKeys.Status] as? String, stat == Constants.FlickrResponseValues.OKStatus else {
                displayError("Flickr API returned an error. See error code and message in \(parsedResult)")
                return
            }
            
            /* GUARD: Is the "photos" key in our result? */
            guard let photosDictionary = parsedResult[Constants.FlickrResponseKeys.Photos] as? [String:AnyObject] else {
                displayError("Cannot find key '\(Constants.FlickrResponseKeys.Photos)' in \(parsedResult)")
                return
            }
            
            /* GUARD: Is the "photo" key in photosDictionary? */
            guard let photosArray = photosDictionary[Constants.FlickrResponseKeys.Photo] as? [[String: AnyObject]] else {
                displayError("Cannot find key '\(Constants.FlickrResponseKeys.Photo)' in \(photosDictionary)")
                return
            }
``` swift
            
            if photosArray.count == 0 {
                displayError("No Photos Found. Search Again.")
                return
            } else {
                let randomPhotoIndex = Int(arc4random_uniform(UInt32(photosArray.count)))
                let photoDictionary = photosArray[randomPhotoIndex] as [String: AnyObject]
                let photoTitle = photoDictionary[Constants.FlickrResponseKeys.Title] as? String
```

``` swift
                /* GUARD: Does our photo have a key for 'url_m'? */
                guard let imageUrlString = photoDictionary[Constants.FlickrResponseKeys.MediumURL] as? String else {
                    displayError("Cannot find key '\(Constants.FlickrResponseKeys.MediumURL)' in \(photoDictionary)")
                    return
                }
```

``` swift
                // if an image exists at the url, set the image and title
                let imageURL = URL(string: imageUrlString)
                if let imageData = try? Data(contentsOf: imageURL!) {
                    performUIUpdatesOnMain {
                        self.setUIEnabled(true)
                        self.photoImageView.image = UIImage(data: imageData)
                        self.photoTitleLabel.text = photoTitle ?? "(Untitled)"
                    }
                } else {
                    displayError("Image does not exist at \(imageURL)")
                }
            }
        }
        
        // start the task!
        task.resume()
    }
```
``` swift    
    // MARK: Helper for Creating a URL from Parameters
    
    private func flickrURLFromParameters(_ parameters: [String:AnyObject]) -> URL {
        
        var components = URLComponents()
        components.scheme = Constants.Flickr.APIScheme
        components.host = Constants.Flickr.APIHost
        components.path = Constants.Flickr.APIPath
        components.queryItems = [URLQueryItem]()
        
        for (key, value) in parameters {
            let queryItem = URLQueryItem(name: key, value: "\(value)")
            components.queryItems!.append(queryItem)
        }
        
        return components.url!
    }
}
```

In the above example, we created a helper function that allows us to easily break down our requests utilizing URLComponents, avoiding having to manually escape URLS and allowing us to appen the Items from the call into a ready to go container? (Fix later)

``` swift

// MARK: - ViewController: UITextFieldDelegate

extension ViewController: UITextFieldDelegate {
    
    // MARK: UITextFieldDelegate
    
    func textFieldShouldReturn(_ textField: UITextField) -> Bool {
        textField.resignFirstResponder()
        return true
    }
    
    // MARK: Show/Hide Keyboard
    
    func keyboardWillShow(_ notification: Notification) {
        if !keyboardOnScreen {
            view.frame.origin.y -= keyboardHeight(notification)
        }
    }
    
    func keyboardWillHide(_ notification: Notification) {
        if keyboardOnScreen {
            view.frame.origin.y += keyboardHeight(notification)
        }
    }
    
    func keyboardDidShow(_ notification: Notification) {
        keyboardOnScreen = true
    }
    
    func keyboardDidHide(_ notification: Notification) {
        keyboardOnScreen = false
    }
    
    func keyboardHeight(_ notification: Notification) -> CGFloat {
        let userInfo = (notification as NSNotification).userInfo
        let keyboardSize = userInfo![UIKeyboardFrameEndUserInfoKey] as! NSValue
        return keyboardSize.cgRectValue.height
    }
    
    func resignIfFirstResponder(_ textField: UITextField) {
        if textField.isFirstResponder {
            textField.resignFirstResponder()
        }
    }
    
    @IBAction func userDidTapView(_ sender: AnyObject) {
        resignIfFirstResponder(phraseTextField)
        resignIfFirstResponder(latitudeTextField)
        resignIfFirstResponder(longitudeTextField)
    }
    
    // MARK: TextField Validation
    
    func isTextFieldValid(_ textField: UITextField, forRange: (Double, Double)) -> Bool {
        if let value = Double(textField.text!), !textField.text!.isEmpty {
            return isValueInRange(value, min: forRange.0, max: forRange.1)
        } else {
            return false
        }
    }
    
    func isValueInRange(_ value: Double, min: Double, max: Double) -> Bool {
        return !(value < min || value > max)
    }
}

```
Extensions allow us to add new functionality to an existing class, structure, enumeration, or even protocol type, including the ability to extend types for which you do not have access to the origin source code. Some background, Extensions are similar to categories in Objective-C, however they do not have names. 

Extensions can: 

- Add computed instance properties and computed type properties
- Define instance methods and type methods
- Provide new initializers
- Define subscripts
- Define and use new nested types
- Make an existing type conform to a protocol


``` swift

// MARK: - ViewController (Configure UI)

private extension ViewController {
    
    func setUIEnabled(_ enabled: Bool) {
        photoTitleLabel.isEnabled = enabled
        phraseTextField.isEnabled = enabled
        latitudeTextField.isEnabled = enabled
        longitudeTextField.isEnabled = enabled
        phraseSearchButton.isEnabled = enabled
        latLonSearchButton.isEnabled = enabled
        
        // adjust search button alphas
        if enabled {
            phraseSearchButton.alpha = 1.0
            latLonSearchButton.alpha = 1.0
        } else {
            phraseSearchButton.alpha = 0.5
            latLonSearchButton.alpha = 0.5
        }
    }
}

```

``` swift
// MARK: - ViewController (Notifications)

private extension ViewController {
    
    func subscribeToNotification(_ notification: NSNotification.Name, selector: Selector) {
        NotificationCenter.default.addObserver(self, selector: selector, name: notification, object: nil)
    }
    
    func unsubscribeFromAllNotifications() {
        NotificationCenter.default.removeObserver(self)
    }
}

```