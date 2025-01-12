# Ghost Portal Theme Guide

Because Ghost does not provide an easy way to edit the theme of their portal and search bar, users are forced to use the default white theme. Often times this theme simply does not fit the style you're going for.

In this guide I'll show a method of portal styling that uses code injection. You will be able to edit the style straight from the Ghost admin dashboard; no need to download / upload any themes.

This guide will follow the style I made for my company's blog here: https://blog.nestra.tech/

Feel free to visit that blog and check out how the portal styling looks if you're curious.

## Injecting Styles

Head over to **Settings > Advanced > Code Injection** and enter the **Site Footer** tab.

In the input, paste this style injection code:

```html
<script>
  window.onload = function () {    
    // Inject Portal Styles
    const portal = document.getElementById("ghost-portal-root");
    if (!portal) return;

    const portal_observer = new MutationObserver((mutationsList) => {
      for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
          const frame = portal.querySelector('[title="portal-popup"]');
          if (frame) {
            frame.addEventListener("load", () => {
              if (frame.contentDocument) {
                const styleElement = document.createElement("style");
                styleElement.innerHTML = `
                  ...
                `;
                frame.contentDocument.head.appendChild(styleElement);
              }
            });
          }
        }
      }
    });

    portal_observer.observe(portal, { childList: true, subtree: true });

    // Inject Search Input Styles
    const search = document.getElementById("sodo-search-root");
    if (!search) return;

    const search_observer = new MutationObserver((mutationsList) => {
      for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
          const frame = search.querySelector('[title="portal-popup"]');
          if (frame) {
            frame.addEventListener("load", () => {
              if (frame.contentDocument) {
                const styleElement = document.createElement("style");
                styleElement.innerHTML = `
                  ...
                `;
                frame.contentDocument.head.appendChild(styleElement);
              }
            });
          }
        }
      }
    });

    search_observer.observe(search, { childList: true, subtree: true });
  };
</script>
```

The areas where you see `...` are where your styles go. If you'd like to only style the portal and not the search bar, simply removethe section of the code which injects styling into the search bar. You can do the opposite as well.

## Use Case: Remove "Powered by Ghost" Portal Watermark

The code below only injects code into the portal iframe, so if you'd like to also style the search bar iframe then you'll need to combine this with the code shown above.

The code below ONLY removes the watermark and applies no further styling.

```html
<script>
  window.onload = function () {    
    // Inject Portal Styles
    const portal = document.getElementById("ghost-portal-root");
    if (!portal) return;

    const portal_observer = new MutationObserver((mutationsList) => {
      for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
          const frame = portal.querySelector('[title="portal-popup"]');
          if (frame) {
            frame.addEventListener("load", () => {
              if (frame.contentDocument) {
                const styleElement = document.createElement("style");
                styleElement.innerHTML = `
                  .gh-portal-powered { display: none; }
                `;
                frame.contentDocument.head.appendChild(styleElement);
              }
            });
          }
        }
      }
    });

    portal_observer.observe(portal, { childList: true, subtree: true });
  };
</script>
```

## Use Case: Remove Watermark + Apply Dark Theme to Portal and Search

This is a 1:1 copy of the code that I inject for [my blog](https://blog.nestra.tech/).

You need to get a little hacky if you want to customize the colors. The way I did it was by using Inspect Element to click on elements I'd like to style, and then overwriting their classnames using the code injection.

You'll notice that the search bar styling is a bit wacky, that's because the search bar uses tailwindcss. Because of this I just directly replace tailwind classes.

```html
<script>
  window.onload = function () {
    // Inject Portal Styles
    const portal = document.getElementById("ghost-portal-root");
    if (!portal) return;

    const observer = new MutationObserver((mutationsList) => {
      for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
          const frame = portal.querySelector('[title="portal-popup"]');
          if (frame) {
            frame.addEventListener("load", () => {
              if (frame.contentDocument) {
                const styleElement = document.createElement("style");
                styleElement.innerHTML = `
                  .gh-portal-powered { display: none; }
                  
                  :root {
                    --black: #000;
                    --grey0: #F2F2F2;
                    --grey1: #A6A6A6;
                    --grey2: #F2F2F2;
                    --grey3: #A6A6A6;
                    --grey4: #A6A6A6;
                    --grey5: #686868;
                    --grey6: #A6A6A6;
                    --grey7: #979797;
                    --grey8: #aeaeae;
                    --grey9: #c5c5c5;
                    --grey10: #dcdcdc;
                    --grey11: #e1e1e1;
                    --grey12: #404040;
                    --grey13: #f9f9f9;
                    --grey14: #fbfbfb;
                    --white: #252525;
                    --red: #f02525;
                    --yellow: #FFDC15;
                    --green: #7FC724;
                    --whitergb: 37 37 37;
                  }

                  .gh-portal-btn {
                    transition: all .1s ease;
                  }

                  .gh-portal-btn:hover {
                    background-color: var(--grey12);
                    border-color: var(--grey12);
                  }

                  .gh-portal-popup-container {
                    box-shadow: none;
                    border: 1px solid #404040;
                  }
                `;
                frame.contentDocument.head.appendChild(styleElement);
              }
            });
          }
        }
      }
    });

    observer.observe(portal, { childList: true, subtree: true });

    // Inject Search Styles
    const search = document.getElementById("sodo-search-root");
    if (!search) return;

    const search_observer = new MutationObserver((mutationsList) => {
      for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
          const frame = search.querySelector('[title="portal-popup"]');
          if (frame) {
            frame.addEventListener("load", () => {
              if (frame.contentDocument) {
                const styleElement = document.createElement("style");
                styleElement.innerHTML = `
                  .bg-white {
                    background-color: #252525;
                  }

                  .shadow-xl {
                    box-shadow: none;
                    border: 1px solid #404040;
                  }

                  .bg-neutral-100 {
                    background-color: #252525;
                  }

                  input {
                    background-color: #252525;
                    color: #F2F2F2;
                  }

                  .text-neutral-800 {
                    color: #F2F2F2;
                  }

                  .text-neutral-900 {
                    color: #F2F2F2;
                  }
                `;
                frame.contentDocument.head.appendChild(styleElement);
              }
            });
          }
        }
      }
    });

    search_observer.observe(search, { childList: true, subtree: true });
  };
</script>

```
