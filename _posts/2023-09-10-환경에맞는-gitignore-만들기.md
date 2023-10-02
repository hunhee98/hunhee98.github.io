---
layout: post
title: 환경에 맞는 gitignore 만들어주는 사이트
date: 2023-09-10 15:34 +0900
tags: 
categories:
---
git remote repository에 불필요한 파일들이 업로드되지 않도록 gitignore 파일을 잘 활용할 필요가 있다.   
실제로 최근 프로젝트를 진행하면서 github 저장소에 올라가서는 안되는 보안성 파일들이나 굳이 올라갈 필요가 없는 파일들이 Staging되는 부분이 굉장히 거슬렸다.   

마침 운영체제나 IDE, 사용하는 언어에 맞게 gitignore를 만들어주는 사이트를 찾아서 간단하게 공유해본다.

[gitignore.io](https://www.toptal.com/developers/gitignore/)

![](https://i.imgur.com/5MTPn8B.png)

필자는 Xcode, Swift, Swift Package Manager를 입력해서 사용 중이다.

```
# Created by https://www.toptal.com/developers/gitignore/api/xcode,swift,swiftpackagemanager
# Edit at https://www.toptal.com/developers/gitignore?templates=xcode,swift,swiftpackagemanager

### Swift ###
# Xcode
#
# gitignore contributors: remember to update Global/Xcode.gitignore, Objective-C.gitignore & Swift.gitignore

## User settings
xcuserdata/

## compatibility with Xcode 8 and earlier (ignoring not required starting Xcode 9)
*.xcscmblueprint
*.xccheckout

## compatibility with Xcode 3 and earlier (ignoring not required starting Xcode 4)
build/
DerivedData/
*.moved-aside
*.pbxuser
!default.pbxuser
*.mode1v3
!default.mode1v3
*.mode2v3
!default.mode2v3
*.perspectivev3
!default.perspectivev3

## Obj-C/Swift specific
*.hmap

## App packaging
*.ipa
*.dSYM.zip
*.dSYM

## Playgrounds
timeline.xctimeline
playground.xcworkspace

# Swift Package Manager
# Add this line if you want to avoid checking in source code from Swift Package Manager dependencies.
# Packages/
# Package.pins
# Package.resolved
# *.xcodeproj
# Xcode automatically generates this directory with a .xcworkspacedata file and xcuserdata
# hence it is not needed unless you have added a package configuration file to your project
# .swiftpm

.build/

# CocoaPods
# We recommend against adding the Pods directory to your .gitignore. However
# you should judge for yourself, the pros and cons are mentioned at:
# https://guides.cocoapods.org/using/using-cocoapods.html#should-i-check-the-pods-directory-into-source-control
# Pods/
# Add this line if you want to avoid checking in source code from the Xcode workspace
# *.xcworkspace

# Carthage
# Add this line if you want to avoid checking in source code from Carthage dependencies.
# Carthage/Checkouts

Carthage/Build/

# Accio dependency management
Dependencies/
.accio/

# fastlane
# It is recommended to not store the screenshots in the git repo.
# Instead, use fastlane to re-generate the screenshots whenever they are needed.
# For more information about the recommended setup visit:
# https://docs.fastlane.tools/best-practices/source-control/#source-control

fastlane/report.xml
fastlane/Preview.html
fastlane/screenshots/**/*.png
fastlane/test_output

# Code Injection
# After new code Injection tools there's a generated folder /iOSInjectionProject
# https://github.com/johnno1962/injectionforxcode

iOSInjectionProject/

### SwiftPackageManager ###
Packages
xcuserdata
*.xcodeproj


### Xcode ###

## Xcode 8 and earlier

### Xcode Patch ###
*.xcodeproj/*
!*.xcodeproj/project.pbxproj
!*.xcodeproj/xcshareddata/
!*.xcodeproj/project.xcworkspace/
!*.xcworkspace/contents.xcworkspacedata
/*.gcno
**/xcshareddata/WorkspaceSettings.xcsettings

# End of https://www.toptal.com/developers/gitignore/api/xcode,swift,swiftpackagemanager
```
