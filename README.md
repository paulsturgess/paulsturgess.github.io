# paulsturgess.co.uk

My personal and very simple webpage.

[https://paulsturgess.co.uk](https://paulsturgess.co.uk)

Tech used:

- Plain old HTML
- [Tailwind CSS](https://tailwindcss.com/)
- Javascript (for the theme toggle)

## Tailwind CSS

Run the following to auto generate the new Tailwind styles:

```bash
npx tailwindcss -i css/input.css -o css/styles.css --watch
```

Mobile-first responsive layouts are achieved using breakpoints.

For example `class"p-6 md:p-8"` means use padding 6 by default and once the window width hits 768px, it will use padding 8.

Tailwind CSS is probably overkill for this webpage, but it was fun to use, especially for the theme switcher.

## Theme switcher

Of course this webpage does not _need_ a theme switcher. But I wanted to add a little something interactive.

The selected dark/light theme when the page loads will be chosen based on your system preference via [Window.matchMedia()](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia).

Tailwind supports dark theming via: `class"text-black dark:text-white"` which means the text will be black by default and when the dark theme is enabled, the text color will be white.

The dark theme is enabled by adding a class of `dark` to a parent element. For this webpage, I add it to the HTML element using javascript.

Because the toggle only works with javascript, I hide it if javascript is disabled. This is achieved by adding a `no-js` class to the body element by default and using javascript to remove the class on page load. That means I can use css to hide the toggle if `no-js` is present.

I thought about hiding the theme switcher for screen readers, but decided to leave it in and instead improve the accessibility. So for screen readers it will read out what theme you are going to toggle to when the toggle is activated.
