These are my notes while going through the Mongoose docs
	They include document methods that I feel are important

	.$isDefault(PATH)
		See if the default value is being used

		Ex:
		MyModel = mongoose.model('test', { name: { type: String, default: 'Val '} });
		var m = new MyModel();
		m.$isDefault('name');               // true

		