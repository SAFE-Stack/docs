# How Do I Use Different Bulma Themes?
## Bulmaswatch
[Bulmaswatch](https://jenil.github.io/bulmaswatch/) is a great website for finding free Bulma themes. However, once you decide on what theme to use, visit [this website](https://www.cdnpkg.com/bulmaswatch) to get a CDN link to its CSS file. For this recipe, I will use the [Nuclear theme](https://jenil.github.io/bulmaswatch/nuclear/).

## I am Using the Standard Template
The standard template uses a CDN (Content Delivery Network) link to reference the [Bulma](https://bulma.io/) theme that it uses. Changing the theme then, is as simple as changing this link. Since the class names Bulma uses to style HTML elements remain the same, we don’t need to change anything else.

#### 1. Find the Link
In your `index.html`, find the line that references the Bulma stylesheet that’s used in the template through a CDN link. It will look like the following:
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.8.0/css/bulma.min.css">
```
#### 2. Change the Link
Go ahead and replace this link with the link to the theme that you want to use, which in my case is Nuclear:
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulmaswatch/0.8.1/nuclear/bulmaswatch.min.css">
```

## I am Using the Minimal Template

#### 1. Add Link to CDN
In your `index.html`, add the following line anywhere between the opening and closing `head` tags:
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.8.0/css/bulma.min.css">
```
#### 2. Add Fulma or Feliz.Bulma to the Solution
Read [this recipe](../add-bulma) for the rest of the instructions.

---
And that’s it. You should now see your app styled in accordance with the Bulma theme you’ve just switched to.
