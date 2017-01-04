# How Do I...

This is a collection of questions and code snippets that I found helpful in developing my first iOS app. 

1. [Add a background image to a TableView](#add-a-background-image-to-a-tableview)
2. [Add a background image to a table cell](#add-a-background-image-to-a-table-cell)
3. [Add a header to TableView](#add-a-header-to-a-TableView)
4. [Change the status bar style](#change-the-status-bar-style)
5. [Modify the border of a TextView or TextField](#modify-the-border-of-a-textview-or-textfield)
6. [Convert Unix timestamp to Date](#convert-Unix-timestamp-to-date)
7. [Get a user's timezone](#get-a-users-timezone)
8. [Move a view up only when the keyboard covers an input field](#move-a-view-up-only-when-the-keyboard-covers-an-input-field)
9. [Calculate elapsed time](#calculate-elasped-time)
10. [Convert a date string to a Date](#convert-a-date-string-to-a-date)
11. [Make dynamically sized TableView cells](#make-dynamically-sized-TableView-cells)
12. [Navigate to a specific TabBar tab](#navigate-to-a-specific-tabbar-tab)
13. [Detect UITabBar Selected Index/ Item Changes that is set Programmatically](#detect-uitabBar-selected-index/-item-changes-that-is-set-programmatically)
14. [Get UIColor from HEX](#get-uicolor-from-hex)
15. [Lock orientation to portrait mode](#lock-orientation-to-portrait-mode)

### Add a background image to a TableView
##### Source: [GrokSwift](https://grokswift.com/transparent-table-view/)

```swift
override func viewWillAppear(animated: Bool) {
    self.clearsSelectionOnViewWillAppear = self.splitViewController!.collapsed
    super.viewWillAppear(animated)
    // Add a background view to the table view
    let backgroundImage = UIImage(named: "GrokSwiftLogo500.png")
    let imageView = UIImageView(image: backgroundImage)
  self.tableView.backgroundView = imageView
}
```

### Add a background image to a table cell
##### Source: [StackOverflow](http://stackoverflow.com/questions/24855002/how-do-i-set-the-background-image-size-of-a-table-cell-in-swift)
```swift
let imageView = UIImageView(frame: CGRectMake(10, 10, cell.frame.width - 10, cell.frame.height - 10))
let image = UIImage(named: "Image Name")
imageView.image = image
cell.backgroundView = UIView()
cell.backgroundView!.addSubview(imageView)
```
### Add a header to a TableView
##### Source: [Hacking With Swift](https://www.hackingwithswift.com/example-code/uikit/how-to-add-a-section-header-to-a-table-view)

To just add some text - quick and dirty
```swift
override func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
    return "Section \(section)"
}
```
If you want to return a custom header view with something more than just some text, you should use `viewForHeaderInSection` instead, like this:
##### Source: [StackOverflow](http://stackoverflow.com/questions/31964941/swift-how-to-make-custom-header-for-uitableview)
In `func tableView(tableView: UITableView, viewForHeaderInSection section: Int) -> UIView?` add the following:
```swift
// set the height of the header
self.tableView.sectionHeaderHeight = 70
// create subviews for the header content
let view = UIView(frame: CGRect(x: 0, y: 0, width: tableView.frame.size.width, height: 18))
let label = UILabel(frame: CGRect(x: 20, y: 20, width: 50, height: 50))
label.text = "TEST TEXT"
label.textColor = UIColor.blackColor()
// add the content to the main view
view.addSubview(label)
```
### Change the status bar style
##### Source: [StackOverflow](http://stackoverflow.com/questions/38740648/how-to-set-status-bar-style-in-swift-3)

Override the `preferredStatusBarStyle` property in the ViewController
```swift
override var preferredStatusBarStyle: UIStatusBarStyle {
    return .lightContent
}
```

### Modify the border of a TextView or TextField
##### Source: [StackOverflow](http://stackoverflow.com/questions/38740648/how-to-set-status-bar-style-in-swift-3)
 
```swift
@IBOutlet weak var email: UITextField!
@IBOutlet weak var pass: UITextField!

 override func viewDidLoad() {
   super.viewDidLoad()

  let myColor : UIColor = UIColor.white()
  email.layer.borderColor = myColor.cgColor
  pass.layer.borderColor = myColor.cgColor

   email.layer.borderWidth = 1.0
   pass.layer.borderWidth = 1.0

}
```

### Convert Unix timestamp to Date
##### Source: [StackOverflow](http://stackoverflow.com/questions/29670585/how-to-convert-unix-epoc-time-to-date-and-time-in-ios-swift)
```swift
let epocTime = NSTimeInterval(1429162809359) / 1000
let myDate = NSDate(timeIntervalSince1970:  epocTime)
print("Converted Time \(myDate)")
```

### Get a users timezone
##### Source: [StackOverflow](http://stackoverflow.com/questions/27053135/how-to-get-a-users-time-zone)
[Leo Dabus](http://stackoverflow.com/users/2303865/leo-dabus) provided an excellent and very detailed answer. Below are just some of the highlights. 
```swift
var secondsFromGMT: Int { return NSTimeZone.local.secondsFromGMT() }
secondsFromGMT  // -10800
```
If you need the abbreviation you can do like this:
```swift
var localTimeZoneAbbreviation: String { return NSTimeZone.local.abbreviation(for: Date()) ?? ""}
localTimeZoneAbbreviation  // "GMT-3"
```
If you need the name you can do like this:
```swift
var localTimeZoneName: String { return (NSTimeZone.local as NSTimeZone).name }
localTimeZoneName // "America/Sao_Paulo"
```
### Move a view up only when the keyboard covers an input field
##### Source: [StackOverflow](http://stackoverflow.com/questions/28813339/move-a-view-up-only-when-the-keyboard-covers-an-input-field)
Comments: I tried the answer, at the time of this writing it had 7 votes, that moved the entire view. As one commenter pointed out that is not the optimal solution and it did cause my app to crash and behave unpredictably.

```swift
func registerForKeyboardNotifications(){
    //Adding notifies on keyboard appearing
    NotificationCenter.default.addObserver(self, selector: #selector(keyboardWasShown(notification:)), name: NSNotification.Name.UIKeyboardWillShow, object: nil)
    NotificationCenter.default.addObserver(self, selector: #selector(keyboardWillBeHidden(notification:)), name: NSNotification.Name.UIKeyboardWillHide, object: nil)
}

func deregisterFromKeyboardNotifications(){
    //Removing notifies on keyboard appearing
    NotificationCenter.default.removeObserver(self, name: NSNotification.Name.UIKeyboardWillShow, object: nil)
    NotificationCenter.default.removeObserver(self, name: NSNotification.Name.UIKeyboardWillHide, object: nil)
}

func keyboardWasShown(notification: NSNotification){
    //Need to calculate keyboard exact size due to Apple suggestions
    self.scrollView.isScrollEnabled = true
    var info = notification.userInfo!
    let keyboardSize = (info[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue.size
    let contentInsets : UIEdgeInsets = UIEdgeInsetsMake(0.0, 0.0, keyboardSize!.height, 0.0)

    self.scrollView.contentInset = contentInsets
    self.scrollView.scrollIndicatorInsets = contentInsets

    var aRect : CGRect = self.view.frame
    aRect.size.height -= keyboardSize!.height
    if let activeField = self.activeField {
        if (!aRect.contains(activeField.frame.origin)){
            self.scrollView.scrollRectToVisible(activeField.frame, animated: true)
        }
    }
}

func keyboardWillBeHidden(notification: NSNotification){
    //Once keyboard disappears, restore original positions
    var info = notification.userInfo!
    let keyboardSize = (info[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue.size
    let contentInsets : UIEdgeInsets = UIEdgeInsetsMake(0.0, 0.0, -keyboardSize!.height, 0.0)
    self.scrollView.contentInset = contentInsets
    self.scrollView.scrollIndicatorInsets = contentInsets
    self.view.endEditing(true)
    self.scrollView.isScrollEnabled = false
}

func textFieldDidBeginEditing(_ textField: UITextField){
    activeField = textField
}

func textFieldDidEndEditing(_ textField: UITextField){
    activeField = nil
}
```
#### Another approach
##### Source: [StackOverflow](http://stackoverflow.com/questions/25693130/move-textfield-when-keyboard-appears-swift)
##### Comments:
>**Firstly** the UIKeyboardWillChangeFrameNotification is probably the best >notification as it handles changes that aren't just show/hide but changes due >to keyboard changes (language, using 3rd party keyboards etc.) and rotations too.

>**Secondly** the animation parameters can be pulled from the notification to ensure that animations are properly together.
>There are probably options to clean up this code a bit more especially if you are comfortable with force unwrapping the dictionary code.

```swift
class MyViewController: UIViewController {

// This constraint ties an element at zero points from the bottom layout guide
@IBOutlet var keyboardHeightLayoutConstraint: NSLayoutConstraint?

override func viewDidLoad() {
    super.viewDidLoad()
    // Note that SO highlighting makes the new selector syntax (#selector()) look
    // like a comment but it isn't one
    NotificationCenter.default.addObserver(self, selector: #selector(self.keyboardNotification(notification:)), name: NSNotification.Name.UIKeyboardWillChangeFrame, object: nil)
}

deinit {
    NotificationCenter.default.removeObserver(self)
}

func keyboardNotification(notification: NSNotification) {
    if let userInfo = notification.userInfo {
        let endFrame = (userInfo[UIKeyboardFrameEndUserInfoKey] as? NSValue)?.cgRectValue
        let duration:TimeInterval = (userInfo[UIKeyboardAnimationDurationUserInfoKey] as? NSNumber)?.doubleValue ?? 0
        let animationCurveRawNSN = userInfo[UIKeyboardAnimationCurveUserInfoKey] as? NSNumber
        let animationCurveRaw = animationCurveRawNSN?.uintValue ?? UIViewAnimationOptions.curveEaseInOut.rawValue
        let animationCurve:UIViewAnimationOptions = UIViewAnimationOptions(rawValue: animationCurveRaw)
        if (endFrame?.origin.y)! >= UIScreen.main.bounds.size.height {
            self.keyboardHeightLayoutConstraint?.constant = 0.0
        } else {
            self.keyboardHeightLayoutConstraint?.constant = endFrame?.size.height ?? 0.0
        }
        UIView.animate(withDuration: duration,
                                   delay: TimeInterval(0),
                                   options: animationCurve,
                                   animations: { self.view.layoutIfNeeded() },
                                   completion: nil)
    }
}
```
#### Third Approach
##### Source: [Hacking With Swift](https://www.hackingwithswift.com/example-code/uikit/how-to-adjust-a-uiscrollview-to-fit-the-keyboard)
##### Comments: I implemented their solution and although it did not work I did not notice that it caused any instability in the app.

Add the following to `viewDidLoad` 
```swift
let notificationCenter = NotificationCenter.default
notificationCenter.addObserver(self, selector: #selector(adjustForKeyboard), name: Notification.Name.UIKeyboardWillHide, object: nil)
notificationCenter.addObserver(self, selector: #selector(adjustForKeyboard), name: Notification.Name.UIKeyboardWillChangeFrame, object: nil)
```
Add this method somewhere in your ViewController
```swift
func adjustForKeyboard(notification: Notification) {
    let userInfo = notification.userInfo!

    let keyboardScreenEndFrame = (userInfo[UIKeyboardFrameEndUserInfoKey] as! NSValue).cgRectValue
    let keyboardViewEndFrame = view.convert(keyboardScreenEndFrame, from: view.window)

    if notification.name == Notification.Name.UIKeyboardWillHide {
        yourTextView.contentInset = UIEdgeInsets.zero
    } else {
        yourTextView.contentInset = UIEdgeInsets(top: 0, left: 0, bottom: keyboardViewEndFrame.height, right: 0)
    }

    yourTextView.scrollIndicatorInsets = yourTextView.contentInset

    let selectedRange = yourTextView.selectedRange
    yourTextView.scrollRangeToVisible(selectedRange)
}
```

##### Source: [GlobalNerdy](http://www.globalnerdy.com/2015/01/29/how-to-work-with-dates-and-times-in-swift-part-two-calculations-with-dates/)
Comments: A couple of options here from a few different sources.

Get the number of days between two dates
```swift
// How many days between Valentine's Day and St. Patrick's Day?
let dayCalendarUnit: NSCalendarUnit = [.Day]
let stPatricksValentinesDayDifference = userCalendar.components(
  dayCalendarUnit,
  fromDate: valentinesDay,
  toDate: stPatricksDay,
  options: [])
// The result should be 31
stPatricksValentinesDayDifference.day
```


### Convert a date string to a Date
##### Source: [StackOverflow](http://stackoverflow.com/questions/24777496/how-can-i-convert-string-date-to-nsdate)
Comments: see http://userguide.icu-project.org/formatparse/datetime for formatting codes
```swift
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy-MM-dd kk:mm:ss" //kk for 24h 
let date = dateFormatter.date(from: "2017-01-02 11:34:45")
```

Or if you want to return a timestamp from a date string
```swift
func convertDateFormatter(date: String) -> String
{

    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss.SSSZ"//this your string date format
    dateFormatter.timeZone = NSTimeZone(name: "UTC") as TimeZone!
    let date = dateFormatter.date(from: date)


    dateFormatter.dateFormat = "yyyy MMM EEEE HH:mm"///this is you want to convert format
    dateFormatter.timeZone = NSTimeZone(name: "UTC") as TimeZone!
    let timeStamp = dateFormatter.string(from: date!)


    return timeStamp
}
```
If you want to go the extra mile and create ans extension on String
```swift
extension String
{   
    func toDateTime() -> NSDate
    {
        //Create Date Formatter
        let dateFormatter = NSDateFormatter()

        //Specify Format of String to Parse
        dateFormatter.dateFormat = "yyyy-MM-dd hh:mm:ss.SSSSxxx"

        //Parse into NSDate
        let dateFromString : NSDate = dateFormatter.dateFromString(self)!

        //Return Parsed Date
        return dateFromString
    }
}
```
Usage: `var myDate = myDateString.toDateTime()`

### Make dynamically sized TableView cells
##### Source: [Ray Wenderlich](https://www.raywenderlich.com/129059/self-sizing-table-view-cells)
Comments: Not difficult at all but not easily boiled down to a neat snippet. As always RayWenderlich.com has top notch content.

### Navigate to a specific TabBar tab
##### Source:
Comment: Not sure where I got this from
```swift
if self.window!.rootViewController as? UITabBarController != nil {
    let tababarController = self.window!.rootViewController as! UITabBarController
    tababarController.selectedIndex = 2
}
```

### Detect UITabBar Selected Index/ Item Changes that is set Programmatically
##### Source: [StackOverflow](http://stackoverflow.com/questions/30692206/detect-uitabbar-selected-index-item-changes-that-is-set-programmatically)
Comment:

`self.selectedIndex` will not return the selected index right away. To check which item is being pressed, we would need to compare the item with the tabBarItems in our UITabBarController
```swift
override func tabBar(tabBar: UITabBar, didSelectItem item: UITabBarItem!) {
    if item == (self.tabBar.items as! [UITabBarItem])[0]{ 
       //Do something if index is 0
    }
    else if item == (self.tabBar.items as! [UITabBarItem])[1]{
       //Do something if index is 1
    }
}
```
### Get UIColor from HEX
##### Source: [Coder Wall](https://coderwall.com/p/6rfitq/ios-ui-colors-with-hex-values-in-swfit)
Comments: Nice little function that returns a UIColor from a HEX value
```swift
func UIColorFromHex(rgbValue:UInt32, alpha:Double=1.0)->UIColor {
    let red = CGFloat((rgbValue & 0xFF0000) >> 16)/256.0
    let green = CGFloat((rgbValue & 0xFF00) >> 8)/256.0
    let blue = CGFloat(rgbValue & 0xFF)/256.0

    return UIColor(red:red, green:green, blue:blue, alpha:CGFloat(alpha))
    
    // rbgValue - define hex color value
    // alpha - define transparency value
    // returns - CGColor
}
```
Usage:
`
// set background color
loadingView.backgroundColor = UIColorFromHex(0x444444, alpha: 0.7)
`
### Lock orientation to portrait mode
##### Source: [StackOverflow](http://stackoverflow.com/questions/28938660/how-to-lock-orientation-of-one-view-controller-to-portrait-mode-only-in-swift)
Comment: The accepted answer relies on a setting in XCode that is no longer available. The next answer worked without issue. Add this code to every view that needs to be locked into a specific orientation.
```swift
override func viewDidLoad() {
    super.viewDidLoad()

    // Force the device in portrait mode when the view controller gets loaded
    UIDevice.currentDevice().setValue(UIInterfaceOrientation.Portrait.rawValue, forKey: "orientation") 
}

override func shouldAutorotate() -> Bool {
    // Lock autorotate
    return false
}

override func supportedInterfaceOrientations() -> Int {

    // Only allow Portrait
    return Int(UIInterfaceOrientationMask.Portrait.rawValue)
}

override func preferredInterfaceOrientationForPresentation() -> UIInterfaceOrientation {

    // Only allow Portrait
    return UIInterfaceOrientation.Portrait
}
```
In your AppDelegate - set supportedInterfaceOrientationsForWindow to whatever orientations you want the entire application to support:
```swift
func application(application: UIApplication, supportedInterfaceOrientationsForWindow window: UIWindow?) -> UIInterfaceOrientationMask {
    return UIInterfaceOrientationMask.All
} 
```



*This document was created with [Dillinger](http://dillinger.io). Dillinger is a cloud-enabled, mobile-ready, offline-storage, AngularJS powered HTML5 Markdown editor. And of course Dillinger itself is open source with a [public repository][dill] on GitHub.*


