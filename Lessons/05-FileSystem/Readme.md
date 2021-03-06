# FileSystem and Review

## Minute-by-Minute

| **Elapsed** | **Time**  | **Activity**              |
| ----------- | --------- | ------------------------- |
| 0:00        | 0:05      | Objectives                |
| 0:05        | 0:20      | Overview                  |
| 0:25        | 0:15      | In Class Activity I       |
| 0:40        | 0:15      | Overview                  |
| 0:55        | 0:10      | BREAK                     |
| 1:05        | 0:40      | In Class Activity II      |
| 1:45        | 0:05      | Wrap Up                   |
| TOTAL       | 1:50      |                           |

## Why you should know this (5 min)

Knowing about how the file system works in an iOS app will enable us to correctly manage how we store and access data. There's a lot of things to consider when using the file system, and as iOS developers it is in our best interest to responsibly handle these resources. This includes not abusing how much we save into files, using the correct file hierarchy and storing data only in files we are allowed to.


## Learning Objectives (5 min)

- Identify the parts of the filesystem in an iOS app.
- Access, store & edit data from the filesystem.
- Combine persistence methods with the filesystem.

## Overview

### Filesystem

A file system handles the persistent storage of data files, apps, and the files associated with the operating system itself.
Therefore, the file system is one of the fundamental resources used by all processes.

Important things to consider:
- Because the number of files can easily be many millions, the file system uses directories to create a hierarchical organization.
- Make sure that the user's files remain easily discoverable and that the files your code uses internally are kept out of the user's way.
- Users of iOS devices do not have direct access to the file system and apps are expected to follow this convention.
- Apps can only read/write files within their sandbox.


When installing a new app, the installer creates container directories for the app inside the sandbox directory. Each container directory has a specific role.

- The bundle container directory holds the app's bundle.  
- The data container directory holds data for both the app and the user. This can further be divided into a number of subdirectories that the app can use to sort and organize its data.
- The app may also request access to additional container directories—for example, the iCloud container—at runtime.

These container directories constitute the app’s primary view of the file system

![App structure](app-structure.png)

#### Important Directories

**Main App Bundle**

```swift
<Application_Home>/AppName.app
```

This is the main bundle of the App.

All of the content of this folder is created at compile time (based on files added in
Xcode) and cannot be modified at runtime!

Bundle contains binary along with resource files

You cannot write to this directory.


**Documents Directory**

```swift
<Application_Home>/Documents/
```

This is the place to store data that is critical to your application
(user generated content, content that cannot be automatically
regenerated)

This directory is backed up with iTunes Backups and it can be
made available to iTunes.


**Temporary Directory**

```swift
<Application_Home>/tmp
```

Use this to store files that you do not need to persist
permanently.

You are responsible for removing files from this
directory when they are no longer needed.

The system may purge this directory when your app is not running.


**Library Directory**

```swift
<Application_Home>/Library
```
Used to store files that you don't want to expose to the user

**Library Cache**

```swift
<Application_Home>/Library/Caches
```

This folder should be used to store cached information (e.g.
profile pictures that have been downloaded).

iOS will remove files from this directory when it needs to free
disk space.

*Note*

- The content of this folder is not backed up to iTunes.

- All files in the Library directory (excluding the Library/Caches
directory) are backed up to iTunes by default. You can change
this by using a file attribute

## In Class Activity I (15 min)

[Worksheet](https://drive.google.com/open?id=19eaA6deNcarLYG32CTksrT1aOcMN-_4br0spyyOn-uo)

Discuss where should you store the following content in an app.

- Drawing created by a user in a graphics app.
- Any data that you don't need to persist for a long time.
- Text files from a notes app.
- Configuration files.
- Data files.
- Downloaded images (ex. profile pictures)
- Videos and audio files.


### Writing files

##### The FileManager

Its a class that allows us to interact with the filesystem.

Writing a file to the documents directory:

```swift
let fileManager = FileManager.default
let urls = fileManager.urls(for: .documentDirectory,
                                    in: .userDomainMask)

if let documentDirectory: URL = urls.first {
    let documentURL = documentDirectory.appendingPathComponent("txtFile.txt")
    // data you want to write to file
    let data: Data? = "Hello World".data(using: .utf8)
    do{
      try data!.write(to: documentURL, options: .atomic)
    }catch{
        // some error    
    }
}
```

## Accessing files

Accessing files within the application bundle:

```swift
let path = Bundle.main.path(forResource: "file1", ofType: ".jpg")
if let path = path {
    let image = UIImage(contentsOfFile: path)
}
```
Short form:
```swift
UIImage(named: "file1.jpg")
```

#### Accessing files within the documents directory:

##### Using the FileManager

```swift
let fileManager = FileManager.default
let urls = fileManager.urls(for: .documentDirectory, in: .userDomainMask)
if let documentDirectory: URL = urls.first {
    let documentURL = documentDirectory.appendingPathComponent("txtFile.txt")
    do {
        print(documentURL)

        let content = try String(contentsOf: documentURL, encoding: .utf8)
        print(content)

    } catch let error {
        print(error.localizedDescription)
    }
}
 ```

Visit the documentation page for info on how to access specific directories in the filesystem

[FileManager Documentation](https://developer.apple.com/documentation/foundation/filemanager)


#### When to use filesystem

- Primarily when storing/caching binary data such as images/ audio/ video
- Can also be used to store serialized Objects, allows to persist objects and Object Graphs from your application


## In Class Activity II

There are ways in which different persistence methods can work together. In this challenge you will use NSCoding and the file system together to add persistence support to an app that reviews meals.

Use [this project](https://developer.apple.com/sample-code/swift/downloads/08_ImplementEditAndDeleteBehavior.zip) that has all the functionality you need except for persistence. If you delete the app, all the content will be gone. Your job is to fix this.

Follow Apple's guide to lead your way into this task and if you get stuck at some point, there's always the final solution so you can compare it with yours.

[Link to guide](https://developer.apple.com/library/archive/referencelibrary/GettingStarted/DevelopiOSAppsSwift/PersistData.html#//apple_ref/doc/uid/TP40015214-CH14-SW1)

<!--Clone/Download the repo below to get started:

[Bundle Challenge - Robo Profiles](https://github.com/Product-College-Labs/RoboProfiles) -->

## Stretch challenges

1. Figure out a way to list all the contents inside the Documents directory.
1. Can we access and list all the assets inside the Assets.xcassets folder? Why or How?
1. Refactor the last activity to have a proper way to manage persistence in the app.


## Resources

[Slides](https://docs.google.com/presentation/d/1AJy3P2IqTQG4LpCUhodxFwHFIdtucSY5EeBBk0sF5yk/edit?usp=sharing)<br>
[FileManger Documentation](https://developer.apple.com/documentation/foundation/filemanager)<br>
[Apple's File system basics](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)
