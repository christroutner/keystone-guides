# API File and Image Uploads

This guide gives code examples on how to set up an API for File and Image transfers to your KeystoneJS installation. It also shows how to set up the server for
local file and image hosting. These examples contain the same code used by (ConnextCMS)[http://connextcms.com].

## Setup
This guide assumes that you have a working version of [KeystoneJS](https://github.com/keystonejs/keystone). All file path references
assume that you are working from the same directory as the keystone.js file.

# Creating Models
This guide does not treat images differently than generic files, since they are all files to KeystoneJS. The difference is how you use them
on the front end once you retrieve the file from the server. With a little of the modification below, you can create a different file model 
called 'images' and send 'images' to their own directory. 


At any rate, below is an example model that leverages the `Types.File` introduced in KeystoneJS v4.0 Beta. This code should be copied into 
the file `models/FileUpload.js`.

```javascript
var keystone = require('keystone');
var Types = keystone.Field.Types;

/**
 * File Upload Model
 * ===========
 * A database model for uploading images to the local file system
 */

var FileUpload = new keystone.List('FileUpload');

var myStorage = new keystone.Storage({
    adapter: keystone.Storage.Adapters.FS,
    fs: {
        path: keystone.expandPath('./public/uploads/files'), // required; path where the files should be stored
        publicPath: '/public/uploads/files', // path where files will be served
    }
});

FileUpload.add({
        //name: { type: Types.Key, required: true, index: true }, //requiring name breaks image upload.
	name: { type: Types.Key, index: true},
  file: { 
		//type: Types.LocalFile, 
    type: Types.File,
    storage: myStorage
		//dest: 'public/uploads/files', 
		//label: 'File',
		//allowedTypes: [ 'application/pdf', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document', 'text/plain',
    //              'audio/mp3', 'audio/x-m4a', 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
    //               'application/vnd.openxmlformats-officedocument.presentationml.presentation', 'application/x-zip-compressed',
    //               'video/mp4'
    //              ],
		//filename: function(item, file) {
		//	return item.id + '.' + file.extension;
		//}
	},
	createdTimeStamp: { type: String },
  alt1: { type: String },
  attributes1: { type: String },
  category: { type: String },      //Used to categorize widgets.
  priorityId: { type: String },    //Used to prioritize display order.
	parent: { type: String },
	children: { type: String },
  url: {type: String},
  fileType: {type: String}
        
});


FileUpload.defaultColumns = 'name';
FileUpload.register();
```


# Opening an API in KeystoneJS

## Router index.js

## Create API Handler

# Upload a file

## Gotcha: Two POST Calls Needed

# Download a file

# README
This guide was originally inspired by [this gist by Jed Watson](https://gist.github.com/JedWatson/9741171#file-routes-index-js-L24) and 
[this tutorial](http://christroutner.com/blog/post/front-end-widgets-part-1-creating-the-db-model) on my own blog. The technique displayed
here is the same used in the [ConnextCMS](http://connextcms.com) software. ConnextCMS is a front end extension for KeystoneJS that mimicks the
WordPress user interface.