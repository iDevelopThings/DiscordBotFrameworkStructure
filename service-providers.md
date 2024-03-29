---
description: Adding additional functionality handlers
---

# Service Providers

## What is a Service Provider?

A Service Provider is loaded when you boot the framework, this allows you to essentially add any custom systems to the framework.   
For example; If you wanted to run an express web server from your Discord Bot also, you could use a Service Provider.

## How they work?

The service providers by default in the framework handle all of the functionality, for example, our commands are handled by default with a Service Provider and a Handler. Here is what the Provider looks like for that logic:

```javascript
const ServiceProvider = require('./../Base/ServiceProvider');
const _               = require('lodash');

class CommandServiceProvider extends ServiceProvider {

	constructor(bot, instance)
	{
		super(bot, instance);
	}

	handle()
	{
		return this;
	}

	async onBoot()
	{
		let commandsClasses = this.instance.services.FileServiceProvider
			.loadForFrameworkAndBase(
				process.cwd() + '/src/Bot/Commands',
				process.cwd() + '/App/Commands',
			);

		for (let index in commandsClasses) {
			let command                               = new commandsClasses[index]();
			command.bot                               = this.bot;
			command.instance                          = this.instance;
			command.commandPrefix                     = '!';
			this.instance.commands[command.trigger()] = command;

			console.log(`[BOOT] Loaded ${command.name}`);
		}

		console.log(`[BOOT] Loaded ${Object.keys(this.instance.commands).length} Commands into the cache.`);
	}

	async afterBoot()
	{
		this.instance.handler('CommandsHandler').setCommands(this.instance.commands);

		console.log(`[BOOT] Finished loading ${Object.keys(this.instance.commands).length} Commands. Test...`);
	}

}

module.exports = CommandServiceProvider;
```

As you can see here, on load we get all the "Commands" from the commands directory, this includes framework default commands and your own custom commands.  
We then loop through all command files and set them on the bot instance, therefore caching them.

## Available methods:



### onBoot\(\)

This method is called when the framework loads, this will run the code you need when everything else is initiating.

### afterBoot\(\)

This method is called after booting has finished and all other Service Providers/Handlers have completed the "onBoot" phase.

{% hint style="info" %}
You can add any extra methods to the Service Provider and call them when ever you wish
{% endhint %}

### Custom Methods

If we add a method called **"doSomething\(\)**" we are able to call that method like this:

```javascript
this.instance.services.TestServiceProvider.doSomething();
//or
this.instance.service('Test').doSomething();
//or
this.instance.service('TestServiceProvider').doSomething()
```

