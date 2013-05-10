---
layout: post
title: "Getting started with JsTestDriver"
date: 2013-05-08 20:31
comments: true
categories: [JavaScript, Test Driven Development]
---
While reading [_Test-Driven JavaScript Development_] [1] by [Christian Johansen] [2], I decided to give Google's [JsTestDriver] [3] a spin. JsTestDriver is a testing framework for JavaScript.

## The 5,000 Foot View

There are three components involved when using JsTestDriver:

  * The JsTestDriver server
  * The Web Browser
  * The JsTestDriver runner

The JsTestDriver server must be started first from the Terminal. Next, you need to choose the web browser you will use to run your JavaScript tests. You can run your tests in more than one browser but I imagine it will probably get annoying and cluttered. The JsTestDriver runner must be run from your Terminal to see if your tests are passing. With that understanding, let's move on to getting things set up.

## Downloading JsTestDriver

If you have [Homebrew] [4] on your machine, you can run the command:

```
$ brew install js-test-driver
```

## Setting up your Environment

Installing via HomeBrew will install the JAR file in the directory: `/usr/local/Cellar/js-test-driver/1.3.5/libexec/`. You need to set the environment variable `JSTESTDRIVER_HOME` in your `.bash_profile` file:

```
export JSTESTDRIVER_HOME=/usr/local/Cellar/js-test-driver/1.3.5/libexec
```

## Configuring JsTestDriver

Before you run JsTestDriver, you need to specify a configuration file. The default filename that JsTestDriver recognizes is `jstestdriver.conf`. Here is what my configuration file looks like:

```
server: http://localhost:9876

load:
  - src/*.js

test:
  - test/*.js
```

This is a file written in [YAML](http://www.yaml.org/) syntax. I specified a random port 9876 for our server. By default, JsTestDriver will listen on port 4224. Because I specified the `*` character, JsTestDriver will load all the source files from the `src` folder and test files from the `test` directory with a `.js` extension. Be sure to only use spaces in your configuration file; don't use tabs.

## Running the JsTestDriver Server

To run JsTestDriver, you need to type this command in the Terminal:

```
java -jar $JSTESTDRIVER_HOME/JsTestDriver-1.3.5.jar --port 9876
```

We pass the JAR file to the `-jar` argument. In our case, the JsTestDriver file is located inside the directory we specified earlier. If you downloaded the JAR file elsewhere, you'll have to write out the correct path here.

Next, you have to specify the same port you specified in your configuration file.

## Selecting a Browser To Capture

In the web browser or browsers on which you want to run your tests, you need to type `http://localhost:9876` or whatever port number you decided to use. A page will load with two links as shown below. Choose the link to "Capture This Browser".

![](https://lh6.googleusercontent.com/-QL4n4uMiXGw/UYvvFK_B0FI/AAAAAAAAAOo/AE0KV4Lfh-I/w387-h242-no/jstestdriver_capture.png)

If all goes well, you should see a green band informing you of the state of the JsTestDriver server.

![](https://lh3.googleusercontent.com/-9hnWrJQKSls/UYvvIK5TYiI/AAAAAAAAAOw/pkmwON60Gx0/w515-h222-no/jstestdriver_waiting.png)

By the way, if you quit running the JsTestDriver server, the browser will inform you of its connection to the server. It will retry connecting to the server...

![](https://lh3.googleusercontent.com/-bBY7UiPtGDk/UYxEYS_slMI/AAAAAAAAAPY/K6HbTNrJslY/w533-h238-no/jstestdriver_retry.png)

... before it finally acknowledges that it is dead:

![](https://lh5.googleusercontent.com/-SvSuQxFSD5Y/UYxEWV5EsQI/AAAAAAAAAPQ/FjHbKktVf7Y/w470-h245-no/jstestdriver_dead.png)

One more thing: if you don't need to run your tests in the browser for whatever reason, you can skip this step of capturing the browser.

## A Simple Test File

We are going to reuse the test spec shown on the JsTestDriver project page. We will call this file `greeter_test.js`:

``` javascript
// test/greeter_test.js

GreeterTest = TestCase("GreeterTest");

GreeterTest.prototype.testGreet = function() {
    var greeter = new myapp.Greeter();
    assertEquals("Hello World!", greeter.greet("World"));
}
```

We also could have written it this way:
``` javascript
// test/greeter_test.js

TestCase("GreeterTest",
  "Testing a simple greet function": function() {
    var greeter = new myapp.Greeter();
    assertEquals("Hello World!", greeter.greet("World"));
  }
});
```

## Running the JsTestDriver Runner

At this point, we are ready to run the JsTestDriver runner and see the feedback we get. Our test should fail, and JsTestDriver confirms this when we run the command `java -jar $JSTESTDRIVER_HOME/JsTestDriver-1.3.5.jar --tests all`:

```
$ java -jar /usr/local/Cellar/js-test-driver/1.3.5/libexec/JsTestDriver-1.3.5.jar --tests all
setting runnermode QUIET
E
Total 1 tests (Passed: 0; Fails: 0; Errors: 1) (1.00 ms)
  Chrome 26.0.1410.65 Mac OS: Run 1 tests (Passed: 0; Fails: 0; Errors 1) (1.00 ms)
    GreeterTest.testGreet error (1.00 ms): ReferenceError: myapp is not defined
      ReferenceError: myapp is not defined
          at GreeterTest.testGreet (http://localhost:9876/test/test/greeter_test.js:4:23)
```

Then we write the code to make this test pass:

``` javascript
// src/greeter.js

myapp = {}

myapp.Greeter = function() { };

myapp.Greeter.prototype.greet = function(name) {
    return "Hello " + name + "!";
};
```

Now when you run the java command to run JsTestDriver, you will see your test passing:

```
$ java -jar /usr/local/Cellar/js-test-driver/1.3.5/libexec/JsTestDriver-1.3.5.jar --tests all
setting runnermode QUIET
.
Total 1 tests (Passed: 1; Fails: 0; Errors: 0) (0.00 ms)
  Chrome 26.0.1410.65 Mac OS: Run 1 tests (Passed: 1; Fails: 0; Errors 0) (0.00 ms)
```

## Jstdutil, a Ruby Wrapper

Tired of running the same Java command over and over? Christian Johansen wrote a Ruby wrapper to automate that task. You can find it on Github under the gem name [jstdutil](https://github.com/cjohansen/jstdutil).

Let's include the gem in our Gemfile:

``` ruby
source "https://rubygems.org"

group :test do
  gem 'jstdutil'
end
```

Now run `bundle install` to have bundler grab the gem.

Remember to repeat the same process as when we ran the Java command by hand:

* Run the server in the background
* Capture the browser
* Run the runner

To run the server in the background, we issue the command in the Terminal:

```
$ bundle exec jstestdriver --port 9876
```

Assuming you've captured the browser, you'll then want to run the runner:

```
$ bundle exec jstestdriver --tests all
```

The neat thing about using jstdutil is the colored output on the Terminal. The gem also comes with an autotesting feature. If you want to run the JsTestDriver runner every time the files change, use `jsautotest`:

```
$ bundle exec jsautotest
```

## Conclusion

I hope you now have a better understanding of how to use JsTestDriver. If you found it useful, share it on Twitter or your social networking platform of choice. I'd also love to hear your feedback. Write it below in the comments.

I included a Pitfalls section below to address some common problems when trying to use JsTestDriver.

## Pitfalls

#### Invalid YAML

Make sure you have written valid YAML. You will get parsing errors if you use tabs in your YAML file. The error below occurred because I used tabs to indent before the `-` character when specifying the directories to load.

```
$ java -jar /usr/local/Cellar/js-test-driver/1.3.5/libexec/JsTestDriver-1.3.5.jar --port 9676
while scanning for the next token
found character 	(9 that cannot start any token
org.jvyaml.ScannerException: ScannerException while scanning for the next token we had this found character 	(9 that cannot start any token
	at org.jvyaml.ScannerImpl.fetchMoreTokens(ScannerImpl.java:359)
	at org.jvyaml.ScannerImpl.peekToken(ScannerImpl.java:160)
	at org.jvyaml.ParserImpl$21.produce(ParserImpl.java:410)
	at org.jvyaml.ParserImpl.parseStreamNext(ParserImpl.java:796)
	at org.jvyaml.ParserImpl.peekEvent(ParserImpl.java:747)
	at org.jvyaml.ComposerImpl.composeNode(ComposerImpl.java:106)
	at org.jvyaml.ComposerImpl.composeNode(ComposerImpl.java:168)
	at org.jvyaml.ComposerImpl.composeDocument(ComposerImpl.java:95)
	at org.jvyaml.ComposerImpl.getNode(ComposerImpl.java:71)
	at org.jvyaml.BaseConstructorImpl.getData(BaseConstructorImpl.java:88)
	at org.jvyaml.YAML.load(YAML.java:188)
	at org.jvyaml.YAML.load(YAML.java:165)
	at com.google.jstestdriver.config.YamlParser.parse(YamlParser.java:51)
	at com.google.jstestdriver.config.DefaultConfigurationSource.parse(DefaultConfigurationSource.java:56)
	at com.google.jstestdriver.embedded.JsTestDriverBuilder.setConfigurationSource(JsTestDriverBuilder.java:258)
	at com.google.jstestdriver.Main.main(Main.java:64)
Unexpected Runner Condition: ScannerException while scanning for the next token we had this found character 	(9 that cannot start any token
 Use --runnerMode DEBUG for more information.
```

#### Conflicting Ports

Also make sure that you use the same port in the `--port` argument when you start the JsTestDriver server as the one you specified in your `.conf` file, otherwise you'll get an error about the server not being able to start. The error below occurred because I specified port 6666 in my configuration file but ran JsTestDriver with a different port.

```
$ java -jar /usr/local/Cellar/js-test-driver/1.3.5/libexec/JsTestDriver-1.3.5.jar --port 9876
setting runnermode QUIET
java.lang.RuntimeException: Connection error on: sun.net.www.protocol.http.HttpURLConnection:http://localhost:6666/jstd/gateway
	at com.google.jstestdriver.HttpServer.postJson(HttpServer.java:162)
	at com.google.jstestdriver.action.ConfigureGatewayAction.run(ConfigureGatewayAction.java:70)
	at com.google.jstestdriver.ActionRunner.runActions(ActionRunner.java:81)
	at com.google.jstestdriver.embedded.JsTestDriverImpl.runConfigurationWithFlags(JsTestDriverImpl.java:342)
	at com.google.jstestdriver.embedded.JsTestDriverImpl.runConfiguration(JsTestDriverImpl.java:233)
	at com.google.jstestdriver.Main.main(Main.java:70)
Caused by: java.net.ConnectException: Connection refused
	at java.net.PlainSocketImpl.socketConnect(Native Method)
	at java.net.PlainSocketImpl.doConnect(PlainSocketImpl.java:351)
	at java.net.PlainSocketImpl.connectToAddress(PlainSocketImpl.java:213)
	at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:200)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:432)
	at java.net.Socket.connect(Socket.java:529)
	at java.net.Socket.connect(Socket.java:478)
	at sun.net.NetworkClient.doConnect(NetworkClient.java:163)
	at sun.net.www.http.HttpClient.openServer(HttpClient.java:388)
	at sun.net.www.http.HttpClient.openServer(HttpClient.java:523)
	at sun.net.www.http.HttpClient.<init>(HttpClient.java:227)
	at sun.net.www.http.HttpClient.New(HttpClient.java:300)
	at sun.net.www.http.HttpClient.New(HttpClient.java:317)
	at sun.net.www.protocol.http.HttpURLConnection.getNewHttpClient(HttpURLConnection.java:970)
	at sun.net.www.protocol.http.HttpURLConnection.plainConnect(HttpURLConnection.java:911)
	at sun.net.www.protocol.http.HttpURLConnection.connect(HttpURLConnection.java:836)
	at sun.net.www.protocol.http.HttpURLConnection.getOutputStream(HttpURLConnection.java:1014)
	at com.google.jstestdriver.HttpServer.postJson(HttpServer.java:154)
	... 5 more
Unexpected Runner Condition: Connection error on: sun.net.www.protocol.http.HttpURLConnection:http://localhost:6666/jstd/gateway
 Use --runnerMode DEBUG for more information.
```

## Sources

[1]: http://www.amazon.com/Test-Driven-JavaScript-Development-Developers-Library/dp/0321683919 "Test-Driven JavaScript Development on Amazon"
[2]: http://cjohansen.no/en "Christian Johansen's Blog"
[3]: https://code.google.com/p/js-test-driver/ "JsTestDriver Project Page on Google Code"
[4]: http://mxcl.github.io/homebrew/ "Homebrew page"

1. Getting Started, [https://code.google.com/p/js-test-driver/wiki/GettingStarted](https://code.google.com/p/js-test-driver/wiki/GettingStarted)
2. Need Help with Error Message: java.lang.RuntimeException: Could not start browser CommandLineBrowserRunner [https://groups.google.com/group/js-test-driver/browse_frm/month/2012-05?fwc=1&pli=1](https://groups.google.com/group/js-test-driver/browse_frm/month/2012-05?fwc=1&pli=1)
3. Jstdutil - A Ruby wrapper over JsTestDriver [http://cjohansen.no/en/javascript/jstdutil_a_ruby_wrapper_over_jstestdriver](http://cjohansen.no/en/javascript/jstdutil_a_ruby_wrapper_over_jstestdriver)
