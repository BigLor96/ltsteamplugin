# LuaTools - Fixed by Cair0

LuaTools UI wasn't showing up in Steam even though the backend said everything was fine. Two things were breaking it. Both fixed.

## What was wrong

**1. JS SyntaxError (two files fighting over the same names)**

webkit.js had two apps smushed into one file with no separation. Both used the same names like `pluginName` and `MILLENNIUM_IS_CLIENT_MODULE`. Millennium started loading every `.js` file (used to only load index.js), so both crashed into each other → error before anything could even run.

**Fix:** Wrapped each app in its own little box `(function(){ ... })()` so their names don't clash.

**2. Plug-in didn't actually load the script**

`millennium.add_browser_js("webkit/luatools.js")` now just gives back an ID but doesn't run the script anymore (v3.4/v3.5 behavior). So the UI just silently never loaded.

**Fix:** The backend copies the JS/CSS files into Steam's `steamui/webkit/` folder, then the script grabs the real UI code and runs it. This gets around the broken Millennium thing.

## Heads ups (not problems right now, just stuff to watch)

- To get past the broken Millennium API, the plugin grabs the UI code and runs it on the fly (`eval()`). That's a bit of a hack and not the cleanest way to load code, but it works.
- Steam updates can wipe the files we drop into its `steamui/webkit/` folder, so the UI might stop showing up after an update until the plugin re-copies them.
