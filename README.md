# Android.CI.Jenkins
Android Continuous Integration with Jenkins

"for cleaning old builds and build without unit tests"

gradle clean build -b $WORKSPACE/build.gradle --exclude-task test

 

"for cleaning old builds and build with unit tests"

gradle clean build -b $WORKSPACE/build.gradle
