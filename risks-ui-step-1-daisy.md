# Risks UI @ React. 
Step-by-step guide.

## Why React Router?
We will use *React Router*, so we can navigate between pages.
Or I should say "pseudo pages", as they are actually the same page versions, 
re-rendered when the user is navigating to a new URL.

Another *React Router* feature we need is redirecting the user to a login page, 
served by the Keycloak.

## Why Tailwind CSS & Daisy UI?

Well, using CSS stylesheets allow us to do pretty much EVERYTHING.
But there is a catch:
- you NEED to deal with a lot of differences & quirks of browsers & their versions
- you still NEED to pre-compose a COHERENT low-level "visual language" 
(color palette, angles, line thickness, fonts, spacing, size proportions, dark mode/ hover/ focus variants, ...)

That is pretty much what Tailwind CSS offers by defining a lot of "utility classes".
However, using "naked" Tailwind CSS classes can produce A LOT! of code.
Hence, building React pages or components this way is CUMBERSOME.

In the old days we had, for example, bootstrap. It wat brilliant, but not perfect, because:
- If you needed to step out of the pre-designed components area, you had only raw CSS for the rescue. No coherent, low level, design language.
- On top of CSS, bootstrap added its own JavaScript. React added a different one. What can possibly go wrong here?

Daisy UI is a Tailwind CSS plugin which:
- defines a lot of pre-baked COMPONENTS, ON TOP of "utility classes"
- produces simple, bootstrap-like, classes for the  components (btn, btn-primary, ...) 
- defines complete visual THEMES & allows to build CUSTOM ones,
- does not even touch JavaScript (some people complain, I consider it a big PLUS!). This way you can use the same styles in pure HTML, React, Java Server Pages, Keycloak login screen templates, ... you name it! 

## Create project
Use the command below to create project:

`npx create-react-router risks-ui`

It will create a *risks-ui* sub-folder. 
Let's go inside (`cd risks-ui`) and run `npm install`.
Unfortunately, we might spot some **security vulnerabilities**. 
Easy, we'll deal with it in a moment.
### Use IntelliJ IDEA
Open project with *IntelliJ IDEA*, and add the exclusion to .gitignore:

```
...
# IntelliJ IDEA
/.idea
```

To make *IntelliJ IDEA* format code similarly as *React Router* does,
create *.editorconfig* file with the following content:

```
root = true

[*]
charset = utf-8
end_of_line = lf
indent_size = 2
indent_style = space
insert_final_newline = false
max_line_length = 120
tab_width = 2

[{*.ts,*.tsx}]
ij_html_do_not_indent_children_of_tags = false
ij_html_space_inside_empty_tag = true
ij_typescript_keep_line_breaks = true
ij_typescript_spaces_within_imports = true
ij_typescript_spaces_within_object_type_braces = true
ij_typescript_spaces_within_object_literal_braces = true
```
Now, when you use *Reformat Code* option, it will keep things as they are.
### Deal with security vulnerabilities

If you run `npm audit`, you will know they are related to a vulnerable version of the *esbuild* tool, 
used under the hood by the *react-router* command.

The vulnerabilities are related to unused *esbuild* functions. 
However, we prefer to enforce react-router to use its newer version, 
by adding an *overrides* section to the *package.json*:
```json
{
  "devDependencies": "...",
  "overrides": {
    "vite": {
      "esbuild": "^0.28.0"
    }
  }
}
```
and re-running:
`npm install`

### Deal with outdated packages

If you wish to stay up-to-date, run `npm outdated`. 
You will see newer packages available.

Leave `typescript` and `@types/node` in old versions,
as we need backword compatibility with Node.js LTS (22.*).
We better use the same TypeScript language version the React Router has beed written in.

Though, we can safely upgrade React Router from v7.16.0 to v7.17.0 and run:

```shell
rm -rf node_modules
rm package-lock.json
npm install
```

### Add Daisy UI, as DEV dependency

Run `npm install --save-dev daisyui@latest`

It will add current version of *Daisy UI* to the *package.json* in devDependencies section.

### Change app.css

Now, REPLACE the entire content of ./src/app.css file to this:

```css
@import "tailwindcss";
@plugin "daisyui" {
    themes: light --default, dark --prefersdark;
}
```
From now on you will be able to use both low level Tailwind CSS "utility classes" and DaisyUI "component classes". 
By default, you use "light" theme, and "dark" theme if your OS or Browser prefers the dark mode.
The CSS stylesheet will be automatically added to your application. 
It will only contain the CSS classes you actually use.

### Change welcome screen

Now, REPLACE the entire content of ./src/welcome/welcome.tsx to this:

```tsx
export function Welcome() {
  return (
    <button className="btn btn-primary">
      Hello, Norman!
    </button>
  );
}
```

Notice "btn" and "btn-primary" Daisy UI classes. 
You can read about them here: https://daisyui.com/components/button.

Please remove unnecessary image files as well:

```shell
rm ./src/welcome/logo-dark.svg
rm ./src/welcome/logo-light.svg
```
### Run example in developer mode.

Now you can either open *package.json* in IntelliJ IDEA,
and click green arrow next to "dev" script, or run:

```shell
npm run dev
```
Finally, you may open your browser and go to http://localhost:5174
You should see a purple "Hello, Norman!" button.

### Experiment with themes

Go to https://daisyui.com/docs/themes/#list-of-themes.
Choose another light & dark theme pair, and modify app.css like this:

```css
@import "tailwindcss";
@plugin "daisyui" {
    themes: cyberpunk --default, dim --prefersdark;
}
```

If you know how, switch between OS/browser dark/light modes.
Notice an automatic change of styles. Restore original themes.