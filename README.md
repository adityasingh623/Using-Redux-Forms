# Using Redux-Forms

Firstly, install redux-forms into your project folder using the command, 
```npm install --save redux-form ```. Please check ```package.json``` and see if any any mention of redux-forms was created. 

## STEP 1

Go to your main redux file, in my case it generally is ```index.js``` file, where I import all other reducer files for modularity. Now, redux-form library has already has set up everything for you, as far as calling the redux store with appropritate action and using appropriate reducers are concerned, all we have to do is use this library and wire up the components in the correct way and we are good to go. 

The way to wire in redux-form into our ```index.js``` file in our reducer folder is given below.

```javascript
import { combineReducers } from  "redux";
import { reducer  as  formReducer} from  'redux-form';

export  default  combineReducers({
	form : formReducer,
});
```
Please make sure to that the line 
```javascript 
form : formReducer
```
remains the same and **DO NOT CHANGE IT**.

## STEP 2

After going through all the steps from **STEP 1**, we shall now continue to our step 2. 
Go, the file where you might be needing to implment a form based functionality. And, add the following named imports, to your file.
```javascript
import { Field, reduxForm } from  'redux-form';
```
Now, ```reduxForm``` is something similar to ```connect()``` method from ```react-redux``` library. So, implementation of ```reduxForm``` is absolutly similar to who one would use ```connect()``` function. 

Replace the orignal ```export default <Component name>``` with the following code:
```javascript
export  default  reduxForm({
	form :  'streamCreate'
})(<Your Component Name>);
```
#### Please Ignore the classNames in the following components you can add your own classNames as you want.

Inside the main ```render()``` method of your class, where you want to render a form based component add the following code.
```javascript
<form  className="ui form error"  onSubmit={this.props.handleSubmit(this.onFormSubmit)}>
	<Field  name='title'  component={this.renderInputComponent}  label='Enter Title'/>
	<Field  name='description'  component={this.renderInputComponent}  label ='Enter Description'/>
	<button  className="ui button primary"  >Submit</button>
</form>
```
Now the ```<Form />``` component is Provided to us by redux-form library, you can study about that component further should you please to do it. But, in this article we just concentrate on just the implementation part. 

```name``` prop inside the ```<Form />``` component is important and should be named as to make it clear what the component is rendering out about, the name should be self explanatory. Like in this case I need two input component one for taking in the title and other for taking in the description, hence the names. 

The ```this.renderInputComponent()``` just renders a input component, which I have separated out to maintain readability. 
The method code is given below 
```javascript
renderInputComponent(formProps){
	return (
		<div  className="field">
		<label>{formProps.label}</label>
		<input  {...formProps.input}/>
		</div>
	);
}
```
If you are wondering where does the argument ```formProps``` come from? It is provided to us by the redux-form library. Not sure about it. 
Now if you look closely to the line ```{this.props.handleSubmit(this.onFormSubmit)}>``` the method ```handleSubmit``` is passed on throught the prop by the redux-form library. Just ```console.log(this.props)``` and you would see it. 
```javascript
1.  {array: {…}, anyTouched: false, asyncValidating: false, asyncValidate: ƒ, blur: ƒ, …}

1.  anyTouched:  false
2.  array:  {insert:  ƒ,  move:  ƒ,  pop:  ƒ,  push:  ƒ,  remove:  ƒ, …}
3.  asyncValidate:  ƒ (name, value, trigger)
4.  asyncValidating:  false
5.  autofill:  ƒ ()
6.  blur:  ƒ ()
7.  change:  ƒ ()
8.  clearAsyncError:  ƒ ()
9.  clearFields:  ƒ ()
10.  clearSubmit:  ƒ ()
11.  clearSubmitErrors:  ƒ ()
12.  destroy:  ƒ ()
13.  dirty:  false
14.  dispatch:  ƒ dispatch()
15.  error:  undefined
16.  form:  "streamCreate"
17.  handleSubmit:  ƒ (submitOrEvent)
18.  history:  {length:  2,  action:  'POP',  location:  {…},  createHref:  ƒ,  push:  ƒ, …}
19.  initialValues:  undefined
20.  initialize:  ƒ ()
21.  initialized:  false
22.  invalid:  false
23.  location:  {pathname:  '/streams/new',  search:  '',  hash:  '',  state:  undefined}
24.  match:  {path:  '/streams/new',  url:  '/streams/new',  isExact:  true,  params:  {…}}
25.  pristine:  true
26.  pure:  true
27.  reset:  ƒ ()
28.  resetSection:  ƒ ()
29.  staticContext:  undefined
30.  submit:  ƒ ()
31.  submitAsSideEffect:  false
32.  submitFailed:  false
33.  submitSucceeded:  false
34.  submitting:  false
35.  touch:  ƒ ()
36.  triggerSubmit:  undefined
37.  untouch:  ƒ ()
38.  valid:  true
39.  warning:  undefined
40.  _reduxForm:  null
41.  ref:  (...)
42.  get ref:  ƒ ()
43.  [[Prototype]]:  Object
```
look at number ```17```. 

as far as ```onSubmit``` goes the method : 
```javascript
onFormSubmit(formProps){
	console.log(formProps);
}
```
perform any logic which you see fit for you application here. 
## STEP 3: Validating Inputs

The validation in redux-form runs the instant the form is rendered on the screen and is interacted by the user. 
>Every single time the user interacts with the form, the validate function runs. EVERY SINGLE TIME!

In order to validate user input, we need to initialize a function, outside class scope if you are using class based component. The validate function would look something like this.
```javascript
const  validate = formValues  => {

	//we'll now define error object to show errors
	const  errors = {};
	
	if(!formValues.title){
		//only rin when user did not input title
		errors.title = 'You must enter a title';
	}

	if(!formValues.description){
		errors.description = 'You must enter a description';
	}

	return  errors;

};
```
Remember the ```name``` prop in the ```<Field />``` component, yes the the library would use the name tag in that component to map the appropriate error on to the screen.

In my case I had two inputs, one ```title``` and one ```description``` hence, I defined two errors here, according to their Field component's name. 

Now, update the export default line to this
```javascript
export  default  reduxForm({

	form :  'streamCreate',
	validate :  validate

})(<Your Component Name>);
```
Now update the method, ```this.renderInputComponent()``` to the following

```javascript
renderInputComponent = (formProps) => {
	return (
		<div  className="field">
			<label>{formProps.label}</label>
			<input  {...formProps.input}/>
			{this.renderError(formProps.meta)}
		</div>
	);
}
```
Now you might be wondering, what the hell is this ```formProps.meta```? Just console log it out ( ```console.log(formProps.meta) ```), you'll see two feilds named ```error``` which contains the error string we need to show when user posts a wrong input, and a ```touched``` feild, which just basically shows that is the user interacting with the perticular form or not. 

Now for the ```this.renderError(formProps.meta)```, the method goes as follows 
```javascript
renderError(meta){
	if(meta.touched && meta.error){
		return (
		<div  className="ui error message">
			<div  className="header">
				{meta.error}
			</div>
		</div>
		);
	}
}
```
It is an arrow method to adjust the ```this``` parameter to link this method to this class. 
