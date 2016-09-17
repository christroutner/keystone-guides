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
	name: { type: Types.Key, index: true},
  file: { 
    type: Types.File,
    storage: myStorage
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

A lot of the additional fields like `alt1`, or `category` are metadata and may be unnessessary for your purposes. I put them in the model as suggestions. 
Feel free to take them out, and it won't hurt anything if you leave them in

# Opening an API in KeystoneJS
Now we are going to create an API that can be used to upload and download files to KeystoneJS. This is a two step process. The first step is adding the following lines 
under the *File Upload Route* block to the `routes/index.js` file:

'''javascript
var keystone = require('keystone');
var middleware = require('./middleware');
var importRoutes = keystone.importer(__dirname);

// Common Middleware
keystone.pre('routes', middleware.initLocals);
keystone.pre('render', middleware.flashMessages);

// Import Route Controllers
var routes = {
        views: importRoutes('./views'),
};

// Setup Route Bindings
exports = module.exports = function (app) {
        // Views
        app.get('/', routes.views.index);
        app.get('/blog/:category?', routes.views.blog);
        app.get('/blog/post/:post', routes.views.post);
        app.get('/gallery', routes.views.gallery);
        app.all('/contact', routes.views.contact);

	  //File Upload Route
	  app.get('/api/fileupload/list', keystone.middleware.api, routes.api.fileupload.list);
	  app.get('/api/fileupload/:id', keystone.middleware.api, routes.api.fileupload.get);
	  app.all('/api/fileupload/:id/update', keystone.middleware.api, routes.api.fileupload.update);
	  app.all('/api/fileupload/create', keystone.middleware.api, routes.api.fileupload.create);
	  app.get('/api/fileupload/:id/remove', keystone.middleware.api, routes.api.fileupload.remove);

        // NOTE: To protect a route so that only admins can see it, use the requireUser middleware:
        // app.get('/protected', middleware.requireUser, routes.views.protected);

};
```

The second step is to create the new file `routes/api/fileupload.js`. If the `routes/api` directory doesn't exist,
you'll need to create it. Copy the code below into `routes/api/fileupload.js`.



## Router index.js

## Create API Handler

# Upload a file

## Optional: Turning on CORS

## Gotcha: Two POST Calls Needed

# Download a file

# README
This guide was originally inspired by [this gist by Jed Watson](https://gist.github.com/JedWatson/9741171#file-routes-index-js-L24) and 
[this tutorial](http://christroutner.com/blog/post/front-end-widgets-part-1-creating-the-db-model) on my own blog. The technique displayed
here is the same used in the [ConnextCMS](http://connextcms.com) software. ConnextCMS is a front end extension for KeystoneJS that mimicks the
WordPress user interface.

The latest version of this file can be found in the [christroutner/keystone-guides repository](https://github.com/christroutner/keystone-guides). 