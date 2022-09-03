# BuyButtonStateSynchronizer

If multiple Shopware plugins modify the buy-button's (btn-buy) disabled-state, the plugin that modifies the state last will win.
BuyButtonStateSynchronizer allows multiple plugins to agree with each other on the disabled-state of the buy button.

BuyButtonStateSynchronizer can also show a dialog instead of disabeling the button.
Each plugin can register an action for which a button is rendered in the dialog.
E.g. if your product requires a file upload and the user has not yet uploaded a file
but still presses the buy button the dialog can show a "Upload file" button.

Instead of modifying the buy-button's disabled-state like this:

```javascript
export default class MyPlugin extends Plugin {
  init() {
      ...
      this.buyBtn = document.getElementsByClassName("btn-buy").item(0);
      ...
  }

  setBuyButtonEnabled(enabled) {
      this.buyBtn.disabled = !enabled;
  }
}
```

a compatible plugin should do this:

```javascript
export default class MyPlugin extends Plugin {
  init() {
    ...
    this.buyBtn = document.getElementsByClassName("btn-buy").item(0);
    ...
  }

  setBuyButtonEnabled(enabled) {
    if ("BuyButtonStateSynchronizer" in window.PluginManager.getPluginList()) {
      const synchronizer = window.PluginManager.getPlugin("BuyButtonStateSynchronizer").get("class");
      const id = "my-plugin-id"; // Globally unique ID
      if (enabled) {
        synchronizer.enableBuyButton(id);
      } else {
        synchronizer.disableBuyButton(id, "Button label", () => console.log("Dialog button pressed"))
      }
    } else {
      this.buyBtn.disabled = !enabled; // Fallback if BuyButtonStateSynchronizer plugin is not installed
    }
  }
}
```


