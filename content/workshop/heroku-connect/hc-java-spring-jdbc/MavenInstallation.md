## Maven Installation on MAC OS steps

### Step 1: maven download link. 
	https://maven.apache.org/download.cgi

### Step 2: Once download move apache-moven-3.3.9 folder from downloads to /user/username/
    Use the below command line in terminal.
    Sudo mv /Users/username/Download/apache-moven-3.3.9  /Users/username/

### Step 3: Set system variables
    We have to set the below environment variables, follow the below steps.
    M2_HOME
    PATH

### Step 4: Create .bash_profile

``` bash
   $ touch .bash_profile
```

This profile is hidden. You need to show that profile please use the below command.

``` bash
$ Defaults write com.apple.finder AppleShowAllFiles TRUE
```

Now goto .bash_profile--->open with text editor and set the below variables

```
   export M2_HOME=/Users/username/apache-maven-3.3.9/
   export PATH=$PATH:/Users/username/apache-maven-3.3.9/bin
```
And save the page.

### Step 5: Goto Terminal and check maven is installed or not.

Mvn --version
