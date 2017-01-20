---
{
  "name": "Dialog: Basics",
  "culture": "en-US",
  "description": "The basics of the dialog plugin for Aurelia.",
  "engines" : { "aurelia-doc" : "^1.0.0" },
  "author": {
    "name": "Patrick Walters",
    "url": "http://patrickwalters.net"
  },
  "contributors": [],
  "translators": [],
  "keywords": ["Dialog", "Modal", "JavaScript", "TypeScript"]
}
---

## [Introduction](aurelia-doc://section/1/version/1.0.0)

This article covers the dialog plugin for Aurelia.  This plugin is created for showing dialogs (sometimes referred to as modals) in your application.  The plugin supports the use of dynamic content for all aspects and is easily configurable / overridable.


## [Installing the plugin](aurelia-doc://section/2/version/1.0.0)

1. In your **JSPM**-based project install the plugin via `jspm` with following command

  ```shell
jspm install aurelia-dialog
  ```

If you use **Webpack**, install the plugin with the following command

  ```shell
npm install aurelia-dialog --save
  ```

If you use the **Aurelia CLI**, install the plugin with the following command

  ```shell
npm install aurelia-dialog --save
  ```

and then add a section in your `aurelia.json` for the plugin

<code-listing heading="aurelia.json">
  <source-code lang="ES 2015">
  {
  dependencies: [
  //...
    {
      "name": "aurelia-dialog",
      "path": "../node_modules/aurelia-dialog/dist/amd",
      "main": "aurelia-dialog"
    }
  //...
  ]
}
  </source-code>
</code-listing>

If you use TypeScript, install the plugin's typings with the following command

```shell
typings install github:aurelia/dialog --save
  ```

2. Make sure you use [manual bootstrapping](http://aurelia.io/docs#startup-and-configuration). In order to do so open your `index.html` and locate the element with the attribute aurelia-app. Change it to look like this:

<code-listing heading="index.html">
  <source-code lang="ES 2015">
  <body aurelia-app="main">...</body>
  </source-code>
</code-listing>

3. Create (if you haven't already) a file `main.js` in your `src` folder with following content:

<code-listing heading="main.js">
  <source-code lang="ES 2015">
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin('aurelia-dialog');

    aurelia.start().then(a => a.setRoot());
  }
  </source-code>
</code-listing>

> Note: If you are using WebPack it is possible that the plugin is installed before Aurelia has replaced the `<body>` element, if that is where your `aurelia-app="main"` is defined, which results in some of the dialog components getting overwritten.  In this case you can move the `aurelia-app` attribute to a `<div>` inside of the `<body>`.  Example - `<body><div aurelia-app="main"></div></body>`.

## [Using the plugin](aurelia-doc://section/3/version/1.0.0)

There are a few ways you can take advantage of the Aurelia dialog.

1. You can use the dialog service to open a prompt -

<code-listing heading="welcome.js">
  <source-code lang="ES 2015">
  import {DialogService} from 'aurelia-dialog';
  import {Prompt} from './prompt';
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    submit(){
      this.dialogService.open({ viewModel: Prompt, model: 'Good or Bad?'}).then(response => {
        if (!response.wasCancelled) {
          console.log('good');
        } else {
          console.log('bad');
        }
        console.log(response.output);
      });
    }
  }
  </source-code>
</code-listing>

This will open a prompt and return a promise that `resolve`s when closed.  If the user clicks out, clicks cancel, or clicks the 'x' in the top right it will still `resolve` the promise but will have a property on the response `wasCancelled` to allow the developer to handle cancelled dialogs.

There is also an `output` property that gets returned with the outcome of the user action if one was taken.

2. You can create your own view / view-model and use the dialog service to call it from your app's view-model -

<code-listing heading="welcome.js">
  <source-code lang="ES 2015">
  import {EditPerson} from './edit-person';
  import {DialogService} from 'aurelia-dialog';
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    person = { firstName: 'Wade', middleName: 'Owen', lastName: 'Watts' };
    submit(){
      this.dialogService.open({ viewModel: EditPerson, model: this.person}).then(response => {
        if (!response.wasCancelled) {
          console.log('good - ', response.output);
        } else {
          console.log('bad');
        }
        console.log(response.output);
      });
    }
  }
  </source-code>
</code-listing>

  This will open a dialog and control it the same way as the prompt.  The important thing to keep in mind is you need to follow the same method of utilizing a `DialogController` in your `EditPerson` view-model as well as accepting the model in your activate method -

<code-listing heading="edit-person.js">
  <source-code lang="ES 2015">
  import {DialogController} from 'aurelia-dialog';

  export class EditPerson {
    static inject = [DialogController];
    person = { firstName: '' };
    constructor(controller){
      this.controller = controller;
    }
    activate(person){
      this.person = person;
    }
  }
  </source-code>
</code-listing>

  and the corresponding view -

<code-listing heading="edit-person.html">
  <source-code lang="ES 2015">
  <template>
    <ai-dialog>
      <ai-dialog-body>
        <h2>Edit first name</h2>
        <input value.bind="person.firstName" />
      </ai-dialog-body>

      <ai-dialog-footer>
        <button click.trigger="controller.cancel()">Cancel</button>
        <button click.trigger="controller.ok(person)">Ok</button>
      </ai-dialog-footer>
    </ai-dialog>
  </template>
  </source-code>
</code-listing>

## [Attach Focus custom attribute](aurelia-doc://section/4/version/1.0.0)

The library exposes an `attach-focus` custom attribute that allows focusing in on an element in the modal when it is loaded.  You can use this to focus a button, input, etc...  Example usage -

<code-listing heading="edit-person.html">
  <source-code lang="ES 2015">
  <template>
    <ai-dialog>
      <ai-dialog-body>
        <h2>Edit first name</h2>
        <input attach-focus="true" value.bind="person.firstName" />
      </ai-dialog-body>
    </ai-dialog>
  </template>
  </source-code>
</code-listing>

You can also bind the value of the attach-focus attribute if you want to alter which element will be focused based on a view model property.

<code-listing heading="edit-person.html">
  <source-code lang="ES 2015">
  <input attach-focus.bind="isNewPerson" value.bind="person.email" />
  <input attach-focus.bind="!isNewPerson" value.bind="person.firstName" />
  </source-code>
</code-listing>

## [Settings](aurelia-doc://section/5/version/1.0.0)

### Global Settings

You can specify global settings as well for all dialogs to use when installing the plugin via the configure method. If providing a custom configuration, you *must* call the `useDefaults()` method to apply the base configuration.

<code-listing heading="main.js">
  <source-code lang="ES 2015">
export function configure(aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging()
    .plugin('aurelia-dialog', config => {
      config.useDefaults();
      config.settings.lock = true;
      config.settings.centerHorizontalOnly = false;
      config.settings.startingZIndex = 5;
      config.settings.enableEscClose = true;
    });

  aurelia.start().then(a => a.setRoot());
}
  </source-code>
</code-listing>

> Note: The startingZIndex will only be assignable during initial configuration.  This is because we stack everything on that Z-index after bootstrapping the modal.

### Settings

The settings available for the dialog are set on the dialog controller on a per-dialog basis.
- `lock` makes the dialog modal, and removes the close button from the top-right hand corner. (defaults to true)
- `centerHorizontalOnly` means that the dialog will be centered horizontally, and the vertical alignment is left up to you. (defaults to false)
- `position` a callback that is called right before showing the modal with the signature: `(modalContainer: Element, modalOverlay: Element) => void`. This allows you to setup special classes, play with the position, etc... If specified, `centerHorizontalOnly` is ignored. (optional)
- `ignoreTransitions` is a Boolean you must set to `true` if you disable css animation of your dialog. (optional, default to false)
- `yieldController` is a Boolean you must set to `true` if you want to execute some logic when the dialog gets open and/or get access to the dialog controller
- `rejectOnCancel` is a Boolean you must set to `true` if you want to handle cancellations as rejection. The reason will be instance of `DialogCancelError` - the property `wasCancelled` will be set to `true` and if cancellation data was provided it will be set to the `reason` property.
- `enableEscClose` allows pressings escape to close the modal without `lock: false`. (optional, defaults to true)

> Warning: Plugin authors are advised to be explicit with settings that change behavior(`yieldController` and `rejectOnCancel`).

<code-listing heading="prompt.js">
  <source-code lang="ES 2015">
export class Prompt {
  static inject = [DialogController];

  constructor(controller){
    this.controller = controller;
    this.answer = null;

    controller.settings.lock = false;
    controller.settings.centerHorizontalOnly = true;
  }
}
  </source-code>
</code-listing>

## [Accessing DialogController API](aurelia-doc://section/6/version/1.0.0)

It is possible to resolve and close (using cancel/ok/error methods) dialog in the same context where you open it.

<code-listing heading="welcome.js">
  <source-code lang="ES 2015">
  import {EditPerson} from './edit-person';
  import {DialogService} from 'aurelia-dialog';
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    person = { firstName: 'Wade', middleName: 'Owen', lastName: 'Watts' };
    submit(){
      this.dialogService.open({yieldController: true, viewModel: EditPerson, model: this.person}).then(openDialogResult => {
        // Note you get here when the dialog is opened, and you are able to close dialog
        // Promise for the result is stored in openDialogResult.closeResult property
        openDialogResult.closeResult.then((response) => {

          if (!response.wasCancelled) {
            console.log('good');
          } else {
            console.log('bad');
          }

          console.log(response);

        })

        setTimeout(() => {
          openDialogResult.controller.cancel('canceled outside after 3 sec')
        }, 3000)

      });
    }
  }
  </source-code>
</code-listing>

## [Styling the Dialog](aurelia-doc://section/7/version/1.0.0)

## Overlay with 50% opacity

Bootstrap adds 50% opacity and a background color of black to the modal.  To achieve this in dialog you can simply add the following CSS -

<code-listing heading="welcome.js">
  <source-code lang="ES 2015">
ai-dialog-overlay.active {
  background-color: black;
  opacity: .5;
}
  </source-code>
</code-listing>