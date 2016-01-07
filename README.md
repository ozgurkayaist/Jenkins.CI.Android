
Android Continuous Integration with Jenkins
===================

![enter image description here](https://media.licdn.com/mpr/mpr/jc/AAEAAQAAAAAAAAUrAAAAJDJiZWE2MGMxLTJiZTctNGFjMy04NjMwLTE2ZDdkNDU4ZTBhMQ.jpg)

If you have Jenkins CI @ your company and you want to use it for mobile application integrations,  this article is for you !


----------


First of all, There will be minimum 3 Jenkins freestyle jobs. You should reproduce it with different build settings.

- Build &Unit testing & Code measurements Job
- Creating a Signed APK package Job
- Installing APK package to Real Device or Android Emulator Job


----------


**A. Required Jenkins Plugins**
- Android Emulator Plugin: Starts an Android emulator with given  properties before a build, then shuts it down after.
- Android Lint Plugin: This plugin parses Android Lint analysis results and visualises the issues found.
- Gradle plugin: This plugin allows Jenkins to invoke Gradle build scripts directly.
- JaCoCo plugin: This plugin allows you to capture code coverage report from JaCoCo.
- JUnit Plugin:Allows JUnit-format test results to be published.
 

**B.Prerequisites**
 You must control the following commands are found on your Jenkins master or slave machine. Open the terminal window and run this commands. If an error has occuring during the execution, the commands/application has not installed, or has installed but has not defined in your environment variables

    **Commands:**
    
    "Android": Command opens Android SDK manager. You should create a new Virtual device or install SDK or Intel HAXM.
    
    "adb devices":Lists the connected devices to machine.  ADT Bundle must be installed and configured.
    
    "gradle":Run this command after the gradle installation.
    
    "java -version": check java and JDK installation version and at the Jenkins build server.

    **Variables:**
    
    "ANDROID_HOME" 
    /Users/user/ Library/Android/sdk
    
     "PATH" Variable values must be added new (not delete the old path values): 
    
    /Users/user/Library/Android/sdk/tools
    
    /Users/user/Library/Android/sdk/platform-tools
    
    /Users/user/gradle-2.9/bin
    
    /Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home 

For windows OS: Run "sysdm.cpl"->Click "Advanced" tab->click "Environment Variables" and add/configure them.

 

**C. Build & Run Unit Tests**
- Set your project's source code settings. (Git, TFS, SVN..)
- Define your build triggering options. You should trigger it  periodically or Hooking (Poll SCM with cron string)
 

    Building(hooking) for each developer check-in  Stash  integration example:
    
    Navigate: "YourStashURL/Projects/ProjectName/repos/RepoName /settings/hooks"
    
    "Add hook"->"Stash Webhook to Jenkins"

 Set Poll SCM value to "00110" @Jenkins build job. So the Jenkins job will be triggered automatically for each code checkin operation immediately.

Add build step->"Invoke Gradle Script" and configure it. Or do it manually with Executing shell script as the following commands. Add a Shell script to your Jenkins job.
 

    #for cleaning old builds and build without unit tests
    
    gradle clean build -b $WORKSPACE/build.gradle --exclude-task test
    
     
    
    #for cleaning old builds and build with unit tests
    
    gradle clean build -b $WORKSPACE/build.gradle

(Optional Build Step): You should add code coverage analysis "Jacoco" to your jenkins Gradle build job. There is a best practice @ this url
Add a post build action "Publish Junit test result report"  as the following screenshot. Define the xml files' location that will be used after the gradle build commands has finished.
 

    $WORKSPACE/build/test-results/*.xml

 Add a post build action "Publish Android lint results" as the following screenshot.

 
**D. Create APK package**
You should add the following build step shell scripts to your jenkins job. This job must be trigger manually by the team.

    #clean old builds and build release ready (not running unit tests)
    
    gradle clean assembleRelease -b $WORKSPACE/build.gradle
    
     #sign apk file (You should need to create a keystore only once for this step)
    
    jarsigner -verbose -keystore PATH/TO/YOUR_RELEASE_KEY.keystore -storepass YOUR_STORE_PASS -keypass YOUR_KEY_PASS PATH/TO/YOUR_UNSIGNED_PROJECT.apk YOUR_ALIAS_NAME
    
     #publish apk file
    
    zipalign -v 4 PATH/TO/YOUR_SIGNED_PROJECT.apk PATH/TO/YOUR_SIGNED_AND_ALIGNED_PROJECT.apk



**E. Install Real Android Device(s)**
You should add the "Install Android Package" build step to your jenkins job. Define your APK's full path here. If you connected a real device(s) to your Jenkins slave/master machine, APK will be installed all of them. 
This job must be trigger manually by the team.

You must see your device listed at terminal window when you run "adb devices" command.

You should connect via USB or Wireless debugging options. Make sure that your Jenkins is connected to the same network with your Android device which is connected with wifi. 


**F. Install to Android Emulator(s)**
You should run the Emulator with your properties. Jenkins Job->Build Environment ->"Run an Android emulator during build"

*Warning:Android emulator will be slower than the real devices. You must install Intel HAXM (Hardware Accelerated Execution Manager) to your Jenkins master or slave node.*





> Written with [StackEdit](https://stackedit.io/).
