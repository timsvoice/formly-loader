# formly-loader

Formly-Loader is a complimentry package for the awesome [ Angular Formly ](https://github.com/formly-js/angular-formly)library. Formly-Loader enables you to define your forms in JSON files and import them into your controllers with some minimal configuration. On the simplest level, you define a base path for your JSON forms and bind the result to your Formly $scope object in your controller:

This pattern helps to reduce controller bloat when defining your formly forms and keeps your code more reasonable.

## Dependencies

    "angular": "~1.4",
    "angular-formly": "~6.24"

## Installation

Install with Bower

    bower install angular-formly-loader

Import the script into your app

    <script src="public/lib/angular-formly-loader/formly-loader.js"></script>

Add the formlyLoader factory as a dependency in your Angular controller

## Basic Usage

`form.json`

      [{    
          "key": "username",
          "type": "input",
          "templateOptions": {
              "required": true,
              "label": "Username",
              "type": "text"
          }
      },
      {    
          "key": "password",
          "type": "password",
          "templateOptions": {
              "required": true,
              "label": "Password",
              "type": "text"
          }
      }]

`authentication.controller.js`

	'use strict';

	angular.module('core').controller('HomeController', ['$scope', 'FormlyLoader',
	  function($scope, FormlyLoader) {
	    
	    var self = this;
	    
	    // initiate your model
	    $scope.credentials = {
	        username: '',
	        password: ''
	    }
	    // set the formly base path
	    FormlyLoader.setBasePath('./views/templates/formly-forms/');
	    
	    // the formly load method takes in the name of the form to be loaded and return the form fields using an $http.get request.
	    FormlyLoader.load('form').then(function(formFields) {
	        // bind the formFields to your scope object
	        $scope.form = formFields;          
	    });
	     
	  }  
	]);

`authentication.view.html`
    
    // your formly form in your view
    <form name="userForm" class="signin" novalidate autocomplete="off">
        <formly-form model="credentials" fields="form">
            <button type="submit" id="signin" ng-disabled="userObj.$invalid">Submit</button>
        </formly-form>
    </form>


## Mapping properties

Much of the power of Formly is the declaration of functions and expressions in your form object. This is not possible in straight JSON so formly-loader exposes a setMapping method that recursively processes the $mappings property set in your forms.

`expressive.form.json`

      [{    
          "key": "username",
          "type": "input",
          "templateOptions": {
              "required": true,
              "label": "Username",
              "type": "text"
          }
          // update the model on keyup
          modelOptions: {
            "keyup": "$mappings.keyup"
          }
      },
      {    
          "key": "password",
          "type": "password",
          "templateOptions": {
              "required": true,
              "label": "Password",
              "type": "text"
          }
      }]


`authentication.controller.js`

	'use strict';

	angular.module('core').controller('HomeController', ['$scope', 'FormlyLoader',
	  function($scope, FormlyLoader) {
	    
	    var self = this;
	    
	    // initiate your model
	    $scope.credentials = {
	        username: '',
	        password: ''
	    }
	    
	    // set the formly base path
	    FormlyLoader.setBasePath('./views/templates/formly-forms/');
	    
	    // the formly load method takes in the name of the form 
	    // to be loaded and return the form fields 
	    // using an $http.get request.
	    FormlyLoader.load('form').then(function(formFields) {
	        // bind the formFields to your scope object
	        $scope.form = formFields;          
	    });
	
	    // set the debounce property via the setMappings method
	    // pass in the property you want to set as an argument
	    // and the function will return the value
	    FormlyLoader.setMapping('modelOptions', {
	      keyup: {
	          "updateOn": "keyup",
	          "debounce": 200
	      }        
	    });
	  }  
	]);