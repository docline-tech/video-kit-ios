# Docline Videoconsultation iOS SDK
![](https://drive.google.com/uc?export=view&id=1_VGN5i9_djalUq5SLYMeOCKvZgXteNSI)

The Docline SDK makes it easy and simple to integrate Docline video consultation services.

This app needs access to the camera and microphone to make video consultations.

The plugin [for Capacitor](https://www.npmjs.com/package/capacitor-plugin-docline-sdk) is also available.

Comming soon Swift Package Manager compability.

## Supported
- __iOS 11.0 or higher__

## Example project
The example projects are [here](./Example).  

## Contents
- [Installation](#installation)
- [Additional settings](#additional-settings)
- [Usage](#usage)
- [Core Module](#core-module)
- [Error Module](#error-module)
- [Event Module](#event-module)

# Installation
The iOS SDK can be installed via [Cocoapods][COCOAPODS] and is publicly available on  [Cocoapods][COCOAPODS].

Add this line on your Podfile:

    pod 'DoclineVideoSDK', '1.1.1'

Execute from the project's root folder:

    $ pod install
    
## Removing the iOS SDK from the project

Using [Cocoapods][COCOAPODS].

Remove this line on your Podfile:

    pod 'DoclineVideoSDK', '1.1.1'

Execute from the project's root folder:

    $ pod install

## __Additional settings__
### Add the description of use of the microphone and the camera in the `info.plist` file.
You need to add two entries in the file:
- `NSCameraUsageDescription`, description of the use of the camera.
- ` NSMicrophoneUsageDescription`, description of the use of the microphone.

__info.plist__
![](https://drive.google.com/uc?export=view&id=1QOg43SRokTZ8O9RT1hYoIpsroUYXQkUm)

If your application supports multiple languages, you need to add the translated descriptions in the `Localizable.string` file.

English
```swift
"NSCameraUsageDescription" = "This app needs access to the camera to make video consultations.";
"NSMicrophoneUsageDescription" = "This app needs access to the microphone to make video consultations.";
```

Spanish
```swift
"NSCameraUsageDescription" = "Esta aplicación necesita acceder a la cámara para realizar videoconsultas.";
"NSMicrophoneUsageDescription" = "Esta aplicación necesita acceder al micrófono para realizar videoconsultas.";
```
# Usage

You need to add this line to use it.
```swift
import DoclineVideoSDK
```

## Core Module
Purpose: Generic and miscellaneous functionality.

Remember you can customize the permissions description if needed.

### join()

This method works async. It allows us to connect to the Docline video consultation with the video consultation code.

#### Parameters
- { [Setup](#setup) } setupData - The video consultation setup data
- { [Options](#options) } options - The video consultation options
- { [DoclineDelegate](#general-delegate) } delegate - The general delegate

#### Example usage

```swift
import DoclineVideoSDK

class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    @IBAction func joinButtonAction(_ sender: Any) {
        let setupData = Docline.Setup (serverURL: "serverURL")
        let options = Docline.Options(roomCode: "roomCode")
        Docline.join(setupData, options: options, delegate: self)
    }
}

extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")
        present(sender, animated: true, completion: nil)
    }
    
    func consultationJoinError(_ error: Docline.ResponseError) {
        NSLog("Video consultation failed to join")
    }
    
    func consultationTerminated(_ screenView: Docline.ScreenView) {
        NSLog("Video consultation finished")
        dismiss(animated: true, completion: nil)
    }
}
```
- If no error occurrs them the consultationJoinSuccess delegate triggered.
- If error occurrs them the consultationJoinError delegate triggered.
- If the videoconsultation was finished them the consultationTerminated triggered.

## Error Module
Purpose: Manage the errors that the join method can return.

### Response Error
#### Definition
```swift
public enum ResponseError: Error {
    case unauthorizedError
    case emptyCodeError
    case connectionError
    case customError(String)
    case defaultError
}
```
### consultationJoinError()
Implement the consultationJoinError method of Docline Delegate.
#### Parameters
- { [ResponseError](#response-error) } error - The error response

#### Example usage
```swift
extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")
        present(sender, animated: true, completion: nil)
    }
    
    func consultationJoinError(_ error: Docline.ResponseError) {
        NSLog("Video consultation failed to join")
    }
    
    func consultationTerminated(_ screenView: Docline.ScreenView) {
        NSLog("Video consultation finished")
        dismiss(animated: true, completion: nil)
    }
}
```
## Delegates
Purpose: Manage the events that are emitted in the background, while the framework is running.

The events have been grouped into five groups:

  * [General Delegate.](#general-delegate)
  * [Recording Delegate.](#recording-delegate)
  * [Connection Delegate.](#connection-delegate)
  * [Participant Delegate.](#participant-delegate)
  * [Chat Delegate.](#chat-delegate)


### __Models__
#### Setup
##### Definition
```swift
/**
 * Setup
 */
public struct Setup {
    let serverURL: String
    let primaryColor: UIColor?
    let secondaryColor: UIColor?
    
    public init(
        serverURL: String,
        primaryColor: UIColor? = nil,
        secondaryColor: UIColor? = nil) {
        
        self.serverURL = serverURL
        self.primaryColor = primaryColor
        self.secondaryColor = secondaryColor
    }
}
```
#### Options
##### Definition
```swift
/**
 * Options
 */
public struct Options {
    let roomCode: String
    let enableSetupScreen: Bool
    let enableMicrophone: Bool
    let enableCamera: Bool
    let cameraSource: CameraSource?
    
    public init(
        roomCode: String,
        enableSetupScreen: Bool = true,
        enableMicrophone: Bool = true,
        enableCamera: Bool = true,
        cameraSource: CameraSource = .front) {
        
        self.roomCode = roomCode
        self.enableSetupScreen = enableSetupScreen
        self.enableMicrophone = enableMicrophone
        self.enableCamera = enableCamera
        self.cameraSource = cameraSource
    }
}
```

#### Docline ViewController
##### Definition
```swift
/**
 * Docline ViewController
 */
public class DoclineViewController : DoclineVideoSDK.BaseViewController {
    weak public var delegate: DoclineVideoSDK.DoclineDelegate?
    weak public var recordingDelegate: DoclineVideoSDK.DoclineRecordingDelegate?
    weak public var connectionDelegate: DoclineVideoSDK.DoclineConnectionDelegate?
    weak public var participantDelegate: DoclineVideoSDK.DoclineParticipantDelegate?
    weak public var chatDelegate: DoclineVideoSDK.DoclineChatDelegate?
    override public func setup()
    override public var supportedInterfaceOrientations: UIInterfaceOrientationMask { get }
    override public var preferredStatusBarStyle: UIStatusBarStyle { get }
    override public func viewDidLoad()
}
```

#### Screen View
##### Definition
```swift
/**
 * Screen View
 */
public enum ScreenView {
    case setupScreen, 
         waitingRoom, 
         videoConsultation, 
         permissionsScreen
}
```

#### Camera Source
##### Definition
```swift
/**
 * Camera Source
 */
public enum CameraSource {
    case front, 
         back
}
```
#### Stream Type
##### Definition
```swift
/**
 * Stream Type
 */
public enum StreamType {
    case camera, 
         screen
}
```

#### User Type
##### Definition
```swift
/**
 * Stream Type
 */
public enum UserType {
    case patient, 
         professional
}
```

### __General Delegate__
It´s mandatory implement this delegate for correctly functioning of framework.
You can see a usage example in [join()](#join) method.
#### Definition
```swift
/**
 * Docline Delegate
 */
public protocol DoclineDelegate: AnyObject {
    func consultationJoinSuccess(_ sender: DoclineViewController)
    func consultationJoinError(_ error: Docline.ResponseError)
    func consultationTerminated(_ screenView: Docline.ScreenView)
    func consultationRejoin(_ userType: Docline.UserType)
    func consultationExit(_ userType: Docline.UserType)
    func updatedCamera(_ screenView: Docline.ScreenView, source: Docline.CameraSource)
    func updatedCamera(_ screenView: Docline.ScreenView, isEnabled: Bool)
    func updatedMicrophone(_ screenView: Docline.ScreenView, isEnabled: Bool)
    func show(_ screenView: Docline.ScreenView)
    func consultationJoined()
}
```

### consultationJoinSuccess
Sent when the [`join()`](#join) method completes execution without errors.
#### Parameters
- { [DoclineViewController](#docline-viewcontroller) } sender - The video consultation screen.

### consultationTerminated
Sent when the query has finished, this will release the native component.
#### Parameters
- { [ScreenView](#screen-view) } screenView - The screen where the event occurred

### consultationJoined
Sent upon entering the waiting room.

### show
Sent when a screen loads and indicates the screen is loaded.
#### Parameters
- { [ScreenView](#screen-view) } screenView - The screen where the event occurred

### updatedCamera
Sent when the source of the camera (front or back) is modified and indicates the screen that generated the event.
#### Parameters    
- { [ScreenView](#screen-view) } screenView - The screen where the event occurred
- { [CameraSource](#camera-source) } cameraSource - The source of the selected camera

### updatedCamera
Sent when the status of the camera is modified (enabled or disabled) and indicates the screen that generated the event.
#### Parameters
- { [ScreenView](#screen-view) } screenView - The screen where the event occurred    
- { Boolean } isEnabled - Indicates whether the camera has been enabled or disabled

### updatedMicrophone
Sent when the status of the microphone is modified (enabled or disabled) and indicates the screen that generated the event.
#### Parameters
- { [ScreenView](#screen-view) } screenView - The screen where the event occurred
- { Boolean } isEnabled - Indicates whether the microphone has been enabled or disabled

&nbsp;

### __Recording Delegate__
Before you can use the delegate, you must implement the delegate and configure it as a Docline ViewController. This viewcontroller returns in [consultationJoinSuccess](#consultationjoinsuccess).
#### Definition
```swift
/**
 * Recording Delegate
 */
public protocol DoclineRecordingDelegate: AnyObject {
    func screenRecordingStarted()
    func screenRecordingFinished()
    func screenRecordingApproved()
    func screenRecordingDenied()
}
```
#### Example usage
```swift
extension RecordingDelegate: DoclineRecordingDelegate {
    
    func screenRecordingStarted() {
        NSLog("event: screenRecordingStarted")
    }
...  
}

extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")        
        sender.recordingDelegate = RecordingDelegate()
        present(sender, animated: true, completion: nil)
    }
    ...
```

### screenRecordingStarted
Sent when the video consultation recording begins.
The SDK will automatically ask for user consent and nothing will be recorded until accepted. If the user doesn´t agree, they can exit.

### screenRecordingFinished
Sent when the video consultation recording is finished.

### screenRecordingApproved
Sent when the user accepts the consent of the recording.

### screenRecordingDenied
Sent when the user does not accept the consent of the recording.

&nbsp;

### __Connection Delegate__
Before you can use the delegate, you must implement the delegate and configure it as a Docline ViewController. This viewcontroller returns in [consultationJoinSuccess](#consultationjoinsuccess).
#### Definition
```swift
/**
 * Connection Delegate
 */
public protocol DoclineConnectionDelegate: AnyObject {
    func consultationReconnecting()
    func consultationReconnected()
    func discconectedByError()
    func userSelectExit()
    func userTryReconnect()
}
```
#### Example usage
```swift
extension ConnectionDelegate: DoclineConnectionDelegate {
    
    func consultationReconnecting() {
        NSLog("event: consultationReconnecting")
    }
...  
}

extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")        
        sender.connectionDelegate = ConnectionDelegate()
        present(sender, animated: true, completion: nil)
    }
    ...
```

### consultationReconnecting
Sent when the connection is lost and the plugin tries an automatic recovery.

### consultationReconnected
Sent after the launch of `consultationReconnecting` event, if the connection is recovered.

### discconectedByError
Sent after the launch of the `consultationReconnecting` event, if the connection isn´t recovered.

The SDK will automatically ask users if they want to retry the reconnection or want to exit the video consultation.

### userSelectExit
Sent after the launch of the `discconectedByError` event, if the user decides to exit the video consultation.

### userTryReconnect
Sent after the launch of the `discconectedByError` event, if the user decides to attempt the reconnection.

&nbsp;

### __Participant Delegate__
Before you can use the delegate, you must implement the delegate and configure it as a Docline ViewController. This viewcontroller returns in [consultationJoinSuccess](#consultationjoinsuccess).
#### Definition
```swift
/**
 * Participant Delegate
 */
public protocol DoclineParticipantDelegate: AnyObject {
    func participantConnected(type: Docline.StreamType)
    func participantDisconnected(type: Docline.StreamType)
    func participantSelected(type: Docline.StreamType)
}
```
#### Example usage
```swift
extension ParticipantDelegate: DoclineParticipantDelegate {
    
    func participantConnected(type: Docline.StreamType) {     
        NSLog("event: participantConnected")
    }
...  
}

extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")        
        sender.participantDelegate = ParticipantDelegate()
        present(sender, animated: true, completion: nil)
    }
    ...
```

### participantConnected
Sent when a new participant is connected, this participant can be of camera or screen type.
#### Parameters
- { [StreamType](#stream-type) } participantType - The participant type, can be camera or screen

### participantDisconnected
Sent when a participant disconnects, it can be of camera or screen type.
#### Parameters
- { [StreamType](#stream-type) } participantType - The participant type, can be camera or screen

### participantSelected
Sent when a participant is selected in the list of participants, this participant can be of camera or screen type. The selected participant will go to the main screen of the video consultation.
#### Parameters
- { [StreamType](#stream-type) } participantType - The participant type, can be camera or screen


&nbsp;

### __Chat Delegate__
Before you can use the delegate, you must implement the delegate and configure it as a Docline ViewController. This viewcontroller returns in [consultationJoinSuccess](#consultationjoinsuccess).
#### Definition
```swift
/**
 * Chat Delegate
 */
public protocol DoclineChatDelegate: AnyObject {
    func messageSent()
    func messageReceived()
}
```
#### Example usage
```swift
extension ChatDelegate: DoclineChatDelegate {
    
    func messageSent() {     
        NSLog("event: messageSent")
    }
...  
}

extension ViewController: DoclineDelegate {
    
    func consultationJoinSuccess(_ sender: DoclineViewController) {
        NSLog("Video consultation joined successfully")        
        sender.chatDelegate = ChatDelegate()
        present(sender, animated: true, completion: nil)
    }
    ...
```

### messageSent
Sent when a message is sent in chat.

### messageSent
Sent when a message is received in the chat.


[COCOAPODS]: https://cocoapods.org/
