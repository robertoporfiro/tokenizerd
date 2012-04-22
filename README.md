# tokenizerd - A data tokenization server

tokenizerd presents a websocket API for tokenizing and detokenizing arbitrary
data, represented as JSON key/value pairs.


# Installation

## MongoDB

tokenizerd uses MongoDB as a datastore.  Installation instructions for MongoDB
can be [found here](http://www.mongodb.org/display/DOCS/Quickstart).

Currently tokenizerd connects to MongoDB with no security.  This will be
improved in a future version.

## Go Language

A working installation of [Go](http://golang.org) version 1 is required to
compile tokenizerd.  See the Go [Getting Started](http://golang.org/doc/install)
page for complete instructions.

# tokenizerd

	cd $YOUR_WORKSPACE
	git clone git://github.com/jmcvetta/tokenizerd.git
	cd tokenizerd
	# "go install" may require superuser permissions, depending on how Go
	# is installed on your system.
	go install tokenizerd.go 


# Usage

## Run tokenizerd

By default tokenizerd connects to MongoDB on localhost over the default port,
and listens for websocket connections on ws://localhost:3000.  These can be
changed with command line flags:

	$ ./tokenizerd -help
	Usage of ./tokenizerd:
	  -mongo="localhost": URL of MongoDB server
	  -url="localhost:3000": Host/port on which to run websocket listener

## Connect

Connect to tokenizerd with a websocket client.  You can use [Echo
Test](http://websocket.org/echo.html) to experiment.

## Tokenize

Connect to the websocket:

	ws://localhost:3000/v1/tokenize

Issue a JSON request:

	{
		"ReqId": "an arbitrary string identifying this request",
		"Data": {
			"fieldname1": "fieldvalue1",
			"field name 2": "field  value 2"
		}
	}

Response:

	{
		"ReqId": "an arbitrary string identifying this request",
		"Status": "Success",
		"Error": "",
		"Data": {
			"field name 2": "OTMyMzgzMDAw",
			"fieldname1": "OTMwMjkxMDAw"
		}
	}

## Detokenize

Connect to the websocket:

	ws://localhost:3000/v1/detokenize

Issue a JSON request:

	{
		"ReqId": "foobar",
		"Data": {
			"field name 2": "OTMyMzgzMDAw",
			"fieldname1": "OTMwMjkxMDAw",
			"fieldname 3": "non-existent token string"
		}
	}

Response:

	{
		"ReqId": "foobar",
		"Status": "Success", 
		"Error": "",
		"Data": {
			"field name 2": {
				"Token": "OTMyMzgzMDAw",
				"Found":true,
				"Text": "field value 2"
			},
			"fieldname1": {
				"Token": "OTMwMjkxMDAw",
				"Found":true,
				"Text":"fieldvalue1"
			},
			"fieldname 3": {
				"Token":"non-existent token string",
				"Found": false,
				"Text":""
			}
		}
	}


# License

Tokenizerd is Free Software, released under the terms of the GPL v3.
