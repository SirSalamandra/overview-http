---
title: Navigation.navigate()
slug: Web/API/Navigation/navigate
page-type: web-api-instance-method
tags:
  - API
  - Experimental
  - History
  - Method
  - Navigate
  - Navigation
  - Navigation API
  - Reference
  - Scroll
  - transition
  - Traversal
browser-compat: api.Navigation.navigate
---

{{APIRef("Navigation API")}}{{seecompattable}}

The **`navigate()`** method of the
{{domxref("Navigation")}} interface navigates to a specific URL, updating any provided state in the history entries list.

## Syntax

```js-nolint
navigate(url)
navigate(url, options)
```

### Parameters

- `url`
  - : The destination URL to navigate to.
- `options` {{optional_inline}}
  - : An options object containing the following properties:
    - `state`
      - : Developer-defined information to be stored in the associated {{domxref("NavigationHistoryEntry")}} once the navigation is complete, retrievable via {{domxref("NavigationHistoryEntry.getState", "getState()")}}. This can be any data type. You might, for example, wish to store a page visit count for analytics purposes, or store UI state details so the view can be shown exactly as the user last left it. Any data stored in `state` must be [structured-clonable](/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm).
    - `info`
      - : Developer-defined information to be passed along to the {{domxref("Navigation/navigate_event", "navigate")}} event, made available in {{domxref("NavigateEvent.info")}}. This can be any data type. You might, for example, wish to display newly-navigated content with a different animation depending on how it was navigated to (swipe left, swipe right, or go home). A string indicating which animation to use could be passed in as `info`.
    - `history`
      - : An enumerated value that sets the history behavior of this navigation. The available values are:
        - `auto`: The default value; will usually perform a `push` navigation but will perform a `replace` navigation under special circumstances (see the `NotSupportedError` description below).
        - `push`: Will push a new {{domxref("NavigationHistoryEntry")}} onto the entries list, or fail under special circumstances (see the `NotSupportedError` description below).
        - `replace`: Will replace the current {{domxref("NavigationHistoryEntry")}}.

### Return value

An object with the following properties:

- `committed`
  - : A {{jsxref("Promise")}} which will fulfill when the visible URL has changed and a new {{domxref("NavigationHistoryEntry")}} has been created.
- `finished`
  - : A {{jsxref("Promise")}} which will fulfill when all promises returned by the `intercept()` handler are fulfilled. This is equivalent to the {{domxref("NavigationTransition.finished")}} promise fulfilling, when the {{domxref("Navigation/navigatesuccess_event", "navigatesuccess")}} event fires.

Either one of these promises rejects if the navigation has failed for some reason.

### Exceptions

- `DataCloneError` {{domxref("DOMException")}}
  - : Thrown if the `state` parameter had values included in it that are not structured-clonable.
- `SyntaxError` {{domxref("DOMException")}}
  - : Thrown if the `url` parameter is not a valid URL.
- `NotSupportedError` {{domxref("DOMException")}}
  - : Thrown if the `history` option is set to `push`, and any of the following special circumstances are true:
    - The browser is currently showing the initial `about:blank` document.
    - The current {{domxref("Document")}} is not yet loaded.
    - The `url` parameter is set to the current URL.
    - The `url`'s scheme is `javascript`.

## Examples

### Set up home button

```js
function initHomeBtn() {
  // Get the key of the first loaded entry
  // so the user can always go back to this view.
  const {key} = navigation.currentEntry;
  backToHomeButton.onclick = () => {
    navigation.traverseTo(key);
  }
}
// Intercept navigate events, such as link clicks, and
// replace them with single-page navigations
navigation.addEventListener("navigate", event => {
  event.intercept({
      async handler() {
        // Navigate to a different view,
        // but the "home" button will always work.
      }
  });
});
```

### A smart back button

A page-supplied "back" button can take you back, even after reload, by inspecting the previous history entries:

```js
backButtonEl.addEventListener("click", () => {
  if (navigation.entries()[navigation.currentEntry.index - 1]?.url === "/product-listing") {
    navigation.back();
  } else {
    // If the user arrived here in some other way
    // e.g. by typing the URL directly:
    navigation.navigate("/product-listing", { history: "replace" });
  }
});
```

### Using info and state

```js
async function navigateHandler() {
  await navigation.navigate(url, { info: { animation: "swipe-right" }, state: { infoPaneOpen: true } } );
}
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Modern client-side routing: the Navigation API](https://developer.chrome.com/docs/web-platform/navigation-api/)
- [Navigation API explainer](https://github.com/WICG/navigation-api/blob/main/README.md)
- Domenic Denicola's [Navigation API live demo](https://gigantic-honored-octagon.glitch.me/)
