# Docline Videoconsultation iOS SDK
![](https://drive.google.com/uc?export=view&id=1_VGN5i9_djalUq5SLYMeOCKvZgXteNSI)

The Docline SDK makes it easy and simple to integrate Docline video consultation services.

This app needs access to the camera and microphone to make video consultations.

The plugin [for Capacitor](https://www.npmjs.com/package/capacitor-plugin-docline-sdk) is also available.

## Supported
- __iOS 11.0 or higher__

## Example projects
The example projects are [here](https://gitlab.com/dev-docline/exampledoclinesdk).  

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

    pod 'DoclineVideoSDK', '1.0.9'

Execute from the project's root folder:

    $ pod install
    
## Removing the iOS SDK from the project

Using [Cocoapods][COCOAPODS].

Remove this line on your Podfile:

    pod 'DoclineVideoSDK', '1.0.9'

Execute from the project's root folder:

    $ pod install


[comment]: <> (TODO: Configure Permissions description)

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
- { [Setup](#event-data) } setupData - The video consultation setup data
- { [Options](#event-data) } options - The video consultation options
- { [DoclineDelegate](#event-data) } delegate - The principal delegate

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
---
TODO:

---
## Event Module
Purpose: Manage the events that are emitted in the background, while the plugin is running.

The events have been grouped into five groups:

  * [General Events.](#general-events)
  * [Recording Events.](#recording-events)
  * [Connection Events.](#connection-events)
  * [Participant Events.](#participant-events)
  * [Chat Events.](#chat-events)


### __Event Models__
#### Event Id
##### Definition
```javascript
/**
 * General Event Id
 */
enum EventId {
  // Error Event
  error = "error",
  // General Events
  consultationJoinSuccess = "consultationJoinSuccess", 
  consultationTerminated = "consultationTerminated", 
  showScreenView = "showScreenView", 
  updatedCameraSource = "updatedCameraSource", 
  updatedCameraStatus = "updatedCameraStatus",
  updatedMicrophone = "updatedMicrophone",		
  consultationJoined = "consultationJoined",
  // Recording Events
  screenRecordingStarted = "screenRecordingStarted", 
  screenRecordingFinished = "screenRecordingFinished", 
  screenRecordingApproved = "screenRecordingApproved", 
  screenRecordingDenied = "screenRecordingDenied",
  // Connection Events
  consultationReconnecting = "consultationReconnecting", 
  consultationReconnected = "consultationReconnected", 
  discconectedByError = "discconectedByError",
  userSelectExit = "userSelectExit",
  userTryReconnect = "userTryReconnect",
  // Participant Events
  participantConnected = "participantConnected", 
  participantDisconnected = "participantDisconnected", 
  participantSelected = "participantSelected",
  // Chat Events
  messageSent = "messageSent", 
  messageReceived = "messageReceived"
}
```
#### Event Data
##### Definition
```javascript
/**
 * Event Data
 */
interface EventData {
  // The event id 
  eventId: EventId;
  // The screen id where the event occurred
  screenId?: ScreenId; 
  // The source of the selected camera
  cameraSource?: CameraSource;
  // Indicates whether the microphone/camera has been enabled or disabled
  isEnabled?: boolean;
  // The participant type, can be camera or screen
  participantType?: ParticipantType;
}
```

#### Screen Id
##### Definition
```javascript
/**
 * Screen Id
 */
enum ScreenId { 
  setupScreen = "setupScreen", 
  waitingRoom = "waitingRoom", 
  videoConsultation = "videoConsultation", 
  permissionsScreen = "permissionsScreen"
}
```

#### Camera Source
##### Definition
```javascript
/**
 * Camera Source
 */
enum CameraSource { 
  front = "front", 
  back = "back"
}
```
#### Participant Type
##### Definition
```javascript
/**
 * Participant Type
 */
enum ParticipantType { 
  camera = "camera", 
  screen = "screen"
}
```

### addListener()
Sets a listener for the indicated event.
#### Parameters
- { [EventId](#event-id) } eventName - The event name
- { (event: [EventData](#event-data)) => void } listenerFunc - The event listener

#### Example usage
```javascript
import { DoclineSDKPlugin, EventData, EventId } from 'capacitor-plugin-docline-sdk';
import { Plugins } from '@capacitor/core';
const { DoclineSDK } = Plugins;
const docline: DoclineSDKPlugin = DoclineSDK as DoclineSDKPlugin;

...

configureListener() {
    docline.addListener(EventId.consultationJoinSuccess, this.consultationJoinSuccess);
    docline.addListener(EventId.showScreenView, this.showScreenView);
    docline.addListener(EventId.updatedCameraSource, this.updatedCameraSource);
    docline.addListener(EventId.updatedCameraStatus, this.updatedCameraStatus);
    docline.addListener(EventId.participantConnected, this.participantConnected);
}

// Listeners

consultationJoinSuccess(event: EventData) {
    console.log(`{eventId: ${event.eventId}}`);
}

showScreenView(event: EventData) {
    console.log(`{eventId: ${event.eventId}, screenId: ${event.screenId}}`);
}

updatedCameraSource(event: EventData) {
    console.log(`{eventId: ${event.eventId}, cameraSource: ${event.cameraSource}}`);
}

updatedCameraStatus(event: EventData) {
    console.log(`{eventId: ${event.eventId}, isEnabled: ${event.isEnabled}}`);
}

participantConnected(event: EventData) {
    console.log(`{eventId: ${event.eventId}, type: ${event.participantType}}`);
}
```

### General Events

### consultationJoinSuccess
Sent when the [`join()`](#join) method completes execution without errors.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### consultationTerminated
Sent when the query has finished, this will release the native component.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### consultationJoined
Sent upon entering the waiting room.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
### showScreenView
Sent when a screen loads and indicates the screen is loaded.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ScreenId](#screen-id) } screenId - The screen id where the event occurred

### updatedCameraSource
Sent when the source of the camera (front or back) is modified and indicates the screen that generated the event.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ScreenId](#screen-id) } screenId - The screen id where the event occurred
    - { [CameraSource](#camera-source) } cameraSource - The source of the selected camera

### updatedCameraStatus
Sent when the status of the camera is modified (enabled or disabled) and indicates the screen that generated the event.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ScreenId](#screen-id) } screenId - The screen id where the event occurred
    - { Boolean } isEnabled - Indicates whether the camera has been enabled or disabled

### updatedMicrophone
Sent when the status of the microphone is modified (enabled or disabled) and indicates the screen that generated the event.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ScreenId](#screen-id) } screenId - The screen id where the event occurred
    - { Boolean } isEnabled - Indicates whether the microphone has been enabled or disabled

&nbsp;

### Recording Events

### screenRecordingStarted
Sent when the video consultation recording begins.
The SDK will automatically ask for user consent and nothing will be recorded until accepted. If the user doesn´t agree, they can exit.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### screenRecordingFinished
Sent when the video consultation recording is finished.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### screenRecordingApproved
Sent when the user accepts the consent of the recording.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### screenRecordingDenied
Sent when the user does not accept the consent of the recording.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

&nbsp;

### Connection Events

### consultationReconnecting
Sent when the connection is lost and the plugin tries an automatic recovery.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### consultationReconnected
Sent after the launch of `consultationReconnecting` event, if the connection is recovered.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### discconectedByError
Sent after the launch of the `consultationReconnecting` event, if the connection isn´t recovered.

The SDK will automatically ask users if they want to retry the reconnection or want to exit the video consultation.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### userSelectExit
Sent after the launch of the `discconectedByError` event, if the user decides to exit the video consultation.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### userTryReconnect
Sent after the launch of the `discconectedByError` event, if the user decides to attempt the reconnection.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

&nbsp;

### __Participant Events__

### participantConnected
Sent when a new participant is connected, this participant can be of camera or screen type.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ParticipantType](#participant-type) } participantType - The participant type, can be camera or screen

### participantDisconnected
Sent when a participant disconnects, it can be of camera or screen type.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ParticipantType](#participant-type) } participantType - The participant type, can be camera or screen

### participantSelected
Sent when a participant is selected in the list of participants, this participant can be of camera or screen type. The selected participant will go to the main screen of the video consultation.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id
    - { [ParticipantType](#participant-type) } participantType - The participant type, can be camera or screen


&nbsp;

### __Chat Events__

### messageSent
Sent when a message is sent in chat.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id

### messageSent
Sent when a message is received in the chat.
#### Parameters
- { [EventData](#event-data) } event - The event data
    - { [EventId](#event-id) } eventId - The event id


[COCOAPODS]: https://cocoapods.org/
