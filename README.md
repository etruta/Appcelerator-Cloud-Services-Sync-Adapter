New and improved ACS Alloy Module
===
This is very early hacking, late night work with lots of help and insight from Russ and the Alloy Team.
Doing this work to better familiarize myself with the platform and get stuff done quicker, faster and 
better for my clients.


simple stuff, login a user

    // create a user
    var aUser = Alloy.createModel('User');

    // pass in username and password
    aUser.login("testuserone", "password", {
	    success : function(_user) {
		
		    // user model is returned
		    Ti.API.info(' SUCCESS ' + JSON.stringify(_user));
		    Ti.API.info(' model username ' + _user.get("username"));

            // access to session information
		    Ti.API.info(' stored session ' + _user.retrieveStoredSession());
	    },
	    error : function(_error) {
		    Ti.API.error(' ERROR ' + _error);
	    }
    });


creating an object, works just like the books demo provided; here is using the ACS Place object

	var params = {
		"name" : "Appcelerator Cloud Services",
		"created_at" : "2011-03-22T21:12:14+0000",
		"updated_at" : "2011-03-22T21:12:14+0000",
		"address" : "58 South Park Ave.",
		"city" : "San Francisco",
		"state" : "California",
		"postal_code" : "94107-1807",
		"country" : "United States",
		"website" : "http://www.appcelerator.com",
		"twitter" : "acs",
		"lat" : 37.782227,
		"lng" : -122.393159
	}
	
	
	function testPlaces() {
		
		var aPlace, places;
		
		// create a place object
		aPlace = Alloy.createModel('Place', params);
		// save the object
		aPlace.save();
		
		// create a collection
		places = Alloy.createCollection('Place');
		
		// set up a trigger to print out the results when complete
		places.on("fetch", function() {
			Ti.API.info(' places...' + JSON.stringify(places));
		});
		
		// fetch the data
		places.fetch();


		// can also fetch individual item
		aPlace = Alloy.createModel('Place', {
			id : "SPECIFY PLACE ID"
		});
		aPlace.fetch();
	}
	
You are going to want to hop on over ro app/sync/acs.js to see the beginnings of the code for the adapter

The code for the user model is more interesting since I needed to extend the object to support all of the 
special case methods that the user object supports


	function login(_login, _password, _opts) {
		var self = this;
		this.config.Cloud.Users.login({
			login : _login,
			password : _password
		}, function(e) {
			if (e.success) {
				var user = e.users[0];
				Ti.API.info('Logged in! You are now logged in as ' + user.id);
				
				// return a newly created user object here!!
				_opts.success && _opts.success(new model(user));
			} else {
				Ti.API.error(e);
				// sorry.. error message
				_opts.error && _opts.error((e.error && e.message) || e);
			}
		});
	}

The rest of the model follows the same path; check it out and tell me what you think