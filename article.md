https://suragch.medium.com/how-to-make-http-requests-in-flutter-d12e98ee1cef

How to make HTTP requests in Flutter
Suragch
Suragch

May 18, 2019·4 min read





Making GET, POST, PUT, PATCH, and DELETE requests

This article tells how to make HTTP requests using the http package by the Dart team. We will be using JSONPlaceholder as a target for our API examples below. Thanks to this post for the idea.
GET     /posts
GET     /posts/1
POST    /posts
PUT     /posts/1
PATCH   /posts/1
DELETE  /posts/1
I’ll demonstrate these with a simple app. You can find the code at the end of the article.

Setup
Add the http package dependency in pubspec.yaml.
dependencies:
http: ^0.13.3
Then add the following import at the top of the file where you’ll be making the HTTP requests:
import 'package:http/http.dart';
For the methods below, I’ll use the following URL prefix:
static const urlPrefix = 'https://jsonplaceholder.typicode.com';
GET requests
A GET request is used to get some resource from a server.
Future<void> makeGetRequest() async {
final url = Uri.parse('$urlPrefix/posts');
Response response = await get(url);
print('Status code: ${response.statusCode}');
print('Headers: ${response.headers}');
print('Body: ${response.body}');
}
The get method above is part of the http library and makes the actual request. The response that you get back contains the information you need.
Now replace /posts with /posts/1 in the url. Using /posts returns an array of JSON objects while /posts/1 returns a single JSON object, where 1 is the ID of the post you want to get.
You can use dart:convert to convert the raw JSON string to objects. See Parsing Simple JSON in Flutter and Dart for help with that.
POST request
A POST request is used to create a new resource.
Future<void> makePostRequest() async {
final url = Uri.parse('$urlPrefix/posts');
final headers = {"Content-type": "application/json"};
final json = '{"title": "Hello", "body": "body text", "userId": 1}';
final response = await post(url, headers: headers, body: json);
print('Status code: ${response.statusCode}');
print('Body: ${response.body}');
}
If you run that method you should see the following JSON response:
{
"title": "Hello",
"body": "body text",
"userId": 1,
"id": 101
}
PUT request
A PUT request is meant to replace a resource or create it if it doesn’t exist.
Future<void> makePutRequest() async {
final url = Uri.parse('$urlPrefix/posts/1');
final headers = {"Content-type": "application/json"};
final json = '{"title": "Hello", "body": "body text", "userId": 1}';
final response = await put(url, headers: headers, body: json);
print('Status code: ${response.statusCode}');
print('Body: ${response.body}');
}
Note that the url specified the id of 1 for which post to replace. Then it replaced the entire post with a new value.
You should see the following JSON response from the server, which just repeats the new value:
{
"title": "Hello",
"body": "body text",
"userId": 1,
"id": 1
}
PATCH request
A PATCH request is meant to modify an existing resource.
Future<void> makePatchRequest() async {
final url = Uri.parse('$urlPrefix/posts/1');
final headers = {"Content-type": "application/json"};
final json = '{"title": "Hello"}';
final response = await patch(url, headers: headers, body: json);
print('Status code: ${response.statusCode}');
print('Body: ${response.body}');
}
Notice that the JSON string that is passed in only includes the title, not the other parts like in the PUT example.
You should see a response from the server similar to the following:
{
"userId": 1,
"id": 1
"title": "Hello",
"body": "quia et suscipit\nsuscipit recusandae...",
}
The old body text was not changed.
DELETE request
A DELETE request is of course for deleting resources.
Future<void> makeDeleteRequest() async {
final url = Uri.parse('$urlPrefix/posts/1');
final response = await delete(url);
print('Status code: ${response.statusCode}');
print('Body: ${response.body}');
}
The returned body is just an empty JSON array:
{}
Authentication
Although the demo site we used above did not require it, in real life you need to include authentication headers, especially when doing things other than GET requests. You can add the headers to any request, though usually the basic credentials (username and password) would be added to an initial POST request (to sign in) and then you would use the returned token for subsequent requests.
Basic Authentication
// import 'dart:convert'
// import 'dart:io';
final username = 'username';
final password = 'password';
final credentials = '$username:$password';
final stringToBase64 = utf8.fuse(base64);
final encodedCredentials = stringToBase64.encode(credentials);
final headers = {
HttpHeaders.contentTypeHeader: 'application/json',
HttpHeaders.authorizationHeader: 'Basic $encodedCredentials',
};
In an OAuth setup you might put the client ID and secret in the auth header and the username and password in the body, but this will depend on the server setup.
Note that I used the HttpHeaders constants here, which required the dart:io import. Feel free to use the strings "content-type" and "authorization" instead.
Bearer (token) Authentication
final token = 'WIiOiIxMjM0NTY3ODkwIiwibmFtZSI6Ikpv';
final headers = {
HttpHeaders.contentTypeHeader: 'application/json',
HttpHeaders.authorizationHeader: 'Bearer $token',
};
Developing on localhost
When you are developing on your local machine and testing on the emulator or simulator, you may have to adjust the host IP. That’s because the Android emulator doesn’t use localhost. I usually do something like the following function to get the host name dynamically.
import 'dart:io';
import 'package:flutter/foundation.dart';
String get hostname {
if (Platform.isAndroid) {
return 'http://10.0.2.2:8888';
} else {
return 'http://localhost:8888';
}
}
Related
Parsing Simple JSON in Flutter and Dart
What is the difference between PUT, POST and PATCH?
HTTP server frameworks for Dart
Building a Dart server from scratch
Project code
Here is the full code for your reference:

This article was originally based on an answer I wrote for Stack Overflow.
