---
title: React TypeScript Tutorial
date: 2022-12-16
categories: [React, TypeScript]
tags: [React, TypeScript]     # TAG names should always be lowercase

<author_id>:
  name: Benjamín Zambelli
  twitter: BenJokek
  url: this

img_path: /img/
---

![img-description](/reactplustypescript.png)
<!-- _React + TypeScript_ -->

## Configuring React App

Our React App has to be configured specifically to work with TypeScript:

```shell
npx create-react-app react-typescript-tutorial --template typescript
cd react-typescript-tutorial
code .
```
Now if we expand the source folder we can see the App and index files
with a ".tsx" extension. Now components have a ".tsx" extension instead of
".js" or ".jsx".
Also everything looks the same but if you hover over App you can see that App component
is a function which returns a jsx element and this is called type inference.

![img-description](/cap1.png)

In order to keep working I'm going to remove the header content and the unnecessary imports
at the top. Also create in "src" folder a new folder named "components". There is going to be
the React components but I'll focus in TypeScript.

## Typing Props

If I try to pass a prop as normal I see an error from Typescript:

![img-description](/cap2.png)

To inform the type of props to Typescript we use the "type" keyword, we assign a name for the type
and the object with the key (props name "name") and value (props type which is string).
Like this we have now defined the structure or type of the props that the component will
receive.
To inform TypeScript about this after props parameter within parentheses we specify colon
and then the type name.

![img-description](/cap3.png)

Now among all the benefits we receive, if we try to pass a number as a prop we encounter a pretty early error.

![img-description](/cap4.png)

## Basic Props
