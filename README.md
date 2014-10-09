![travis-ci](https://travis-ci.org/semmons99/firebase-token-generator-ruby.svg?branch=master)

# Firebase Token Generator - Ruby

[Firebase Custom Login](https://www.firebase.com/docs/web/guide/simple-login/custom.html)
gives you complete control over user authentication by allowing you to authenticate users
with secure JSON Web Tokens (JWTs). The auth payload stored in those tokens is available
for use in your Firebase [security rules](https://www.firebase.com/docs/security/api/rule/).
This is a token generator library for Ruby which allows you to easily create those JWTs.


## Installation

The Firebase Ruby token generator is available via `gem`:

```bash
$ gem install firebase_token_generator
```


## A Note About Security

**IMPORTANT:** Because token generation requires your Firebase Secret, you should only generate
tokens on *trusted servers*. Never embed your Firebase Secret directly into your application and
never share your Firebase Secret with a connected client.


## Generating Tokens

To generate tokens, you'll need your Firebase Secret which you can find by entering your Firebase
URL into a browser and clicking the "Secrets" tab on the left-hand navigation menu.

Once you've downloaded the library and grabbed your Firebase Secret, you can generate a token with
this snippet of Ruby code:

```ruby
require "firebase_token_generator"

payload = {:uid => "1", :auth_data => "foo", :other_auth_data => "bar"}

generator = Firebase::FirebaseTokenGenerator.new("<YOUR_FIREBASE_SECRET>")
token = generator.create_token(payload)
```

The payload passed to `create_token()` is made available for use within your
security rules via the [`auth` variable](https://www.firebase.com/docs/security/api/rule/auth.html).
This is how you pass trusted authentication details (e.g. the client's user ID)
to your Firebase security rules. The payload can contain any data of your
choosing, however it must contain a `:uid` key, which must be a string of less
than 256 characters. The generated token must be less than 1024 characters in
total.


## Token Options

A second `options` argument can be passed to `create_token()` to modify how Firebase treats the
token. Available options are:

* **expires** (Integer or DateTime) - A timestamp (as number of seconds since the epoch) or
`DateTime` denoting the time after which this token should no longer be valid.

* **notBefore** (Integer or DateTime) - A timestamp (as number of seconds since the epoch) or
`DateTime` denoting the time before which this token should be rejected by the server.

* **admin** (Boolean) - Set to `true` if you want to disable all security rules for this client.
This will provide the client with read and write access to your entire Firebase.

* **debug** (Boolean) - Set to `true` to enable debug output from your security rules. You should
generally *not* leave this set to `true` in production (as it slows down the rules implementation
and gives your users visibility into your rules), but it can be helpful for debugging.

* **simulate** (Boolean) - If `true`, Firebase will run security rules but not actually make any
data changes. Note that this is internal-only for now.

Here is an example of how to use the second `options` argument:

```ruby
require "firebase_token_generator"

payload = {:uid => "1", :auth_data => "foo", :other_auth_data => "bar"}
options = {:admin => true}

generator = Firebase::FirebaseTokenGenerator.new("<YOUR_FIREBASE_SECRET>")
token = generator.create_token(payload, options)
```
