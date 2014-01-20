Validity
========

Effortless JavaScript form validation and error handling

### Basic Usage

Include the required scripts in your HTML

```html
<script type="text/javascript" src="validator.min.js"></script>
<script type="text/javascript" src="validity.min.js"></script>
```

#### Client-Side Validation

Simply add a `data-validation` attribute to each input field you would like validated. Set each attribute to a JSON-string dictionary, with each key being the validation type you would like performed on the input and the value being the error message you would like to appear on that input if its corresponding validation fails:

```html
<form id="loginForm">
  <input type="email" name="email" data-validation="{'required': 'Please enter your email', 'isEmail': 'Please enter a valid email address'}" />
  <input type="password" name="password" data-validation="{'required': 'Please enter a password'}" />
  <input type="button" value="Submit" onclick="login()" />
</form>
```

Then use JavaScript to validate the form before submission. Validation is performed by calling Validity's `validate` function and passing in the id of the form you would like to validate. This function returns `true` if all validations pass and `false` if any of them fail:

```javascript
function login () {
  if (Validity.validate('loginForm')) {
    // post the form data
  } else {
    console.error("There are errors on the form");
  }
}
```

Validity currently supports the following validation types:

- **required** - ensure the field is not empty
- **isEmail** - ensure the field contains a valid email address
- **isURL** - ensure the field contains a valid URL
- **isIP** - ensure the field contains a valid IP address
- **isAlpha** - ensure the field contains only letters (a-zA-Z)
- **isNumeric** - ensure the field contains only numbers
- **isAlphanumeric** - ensure the field contains only letters and numbers
- **isHexadecimal** - ensure the field contains a hexadecimal number
- **isHexColor** - ensure the field contains a hexadecimal color
- **isLowercase** - ensure the field does not contain any uppercase letters
- **isUppercase** - ensure the field does not contain any lowercase letters
- **isInt** - ensure the field contains an integer
- **isFloat** - ensure the field contains a float
- **isDate** - ensure the field contains a date
- **isCreditCard** - ensure the field contains a credit card number

#### Validity helps display errors reported by ther server too

Add a `data-error` attribute to each input field you would like server-side errors to be shown on. Set each attribute to a JSON-string dictionary, with each key being the error returned by the server and the value being the error message you would like to appear on that input if the server returns that error:

```html
<form id="loginForm">
  <input type="email" name="email" data-error="{'101': 'Invalid username or password'}" data-validation="{'required': 'Please enter your email', 'isEmail': 'Please enter a valid email address'}" />
  <input type="password" name="password" data-validation="{'required': 'Please enter a password'}" />
  <input type="button" value="Submit" onclick="login()" />
</form>
```

When you receive response from your server, simply call Validity's `displayServerErrors` function, passing in the id of the form you would like to target, along with an array containing the error codes returned by the server.

```javascript
function login () {
  if (Validity.validate('loginForm')) {
    $.post('/login', $('#loginForm').serialize()).then(function (response) {
      if (response.success) {
         // We successfully logged in
         window.location.href = '/loggedin';
      } else {
        // Login failed
        Validity.displayServerErrors('loginForm', [response.error.code.toString()]);
      }
    });
  } else {
    console.error("There are errors on the form");
  }
}
```

### Using Custom Error-handling Functions

The default error-handling function used by `validate` and `displayServerErrors` is called ` two things:

1. Adds a "has-error" class to the parent of the input (assumed to be a "form-group" or similar container for the input and its associated elements)
2. Sets the text of the first label in the form-group to the error message

This is designed to work seamlessly with forms built using Twitter Bootstrap. If you would like to use a custom error-handling function, it is very easy to do so. Let's say that instead of using the default error-handler, we would like to add the "has-error" class to the input element itself, and make the error message appear in the input's placeholder. Let's define a function that does this:

```javascript
function customSetError (element, message) {
  element.classList.add('has-error');
  element.setAttribute('placeholder', message);
}
```

Let's define a function that will clear this error state as well:

```javascript
function customClearError (element) {
  element.classList.remove('has-error');
  element.setAttribute('placeholder', '');
}
```

Now we can use these by passing them into `validateWithCustomFunctions` and `displayServerErrorsWithCustomFunctions`:

```javascript
function login () {
  if (Validity.validateWithCustomFunctions('loginForm', customSetError, customClearError)) {
    $.post('/login', $('#loginForm').serialize()).then(function (response) {
      if (response.success) {
         // We successfully logged in
         window.location.href = '/loggedin';
      } else {
        // Login failed
        Validity.displayServerErrors('loginForm', [response.error.code.toString()], customSetError, customClearError);
      }
    });
  } else {
    console.error("There are errors on the form");
  }
}
```

### Tests

I don't have a test suite yet. Help me make one!

### License (MIT)

Copyright (c) 2014 Andrew Finch

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.