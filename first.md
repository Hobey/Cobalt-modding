
ok  
here we go

# modding tutorial #1

______________________________________________________

open Cobalt editor  
open the mod editor (F9)

if there nothing on the screen do  
file -> new (ctrl+n)

type in something for name/description/author and select a type if you want  
set load_group to `Modification - middle`

> setting up the mod in the mod editor  
![](https://i.imgur.com/AAVb3xM.jpg)

file -> save (ctrl+s)  
choose a name for the folder with the mod files and hit save  
go to the created folder (should be in `C:\Users\USER_NAME\Documents\Cobalt\mods_dev\MOD_NAME`)

create a "init.lua" file in that folder and open it with the code editor of your choice (it's nice if it can do lua indentation and syntax highlighting)

paste this (just some sample code that prints THIS IS SPARTA at startup):

```lua
local function init ()
    print("THIS IS SPARTA")
end

hook.add("modInit", init)
```

save the file  
go back to the cobalt editor

workshop (ctrl+w)  
upload to cobalt/cobalt wasd workshop just like you would upload a map (and i think it has to be public if you want to play it with someone)

open in overlay  
subscribe to the mod

close the cobalt editor

> selecting workshop
![](https://i.imgur.com/wWATj12.jpg)

> requesting workshop id
![](https://i.imgur.com/usumrNX.jpg)

> fetched workshop data, typed in a title, submitting workshop data
![](https://i.imgur.com/Y9GaQWA.jpg)

> opening in overlay
![](https://i.imgur.com/KzYe8hZ.jpg)

> subscribing
![](https://i.imgur.com/OBKOTBK.jpg)

> done with the cobalt editor
![](https://i.imgur.com/LfPDxkC.jpg)

open cobalt / cobalt WASD (for whichever workshop you uploaded the mod)

goto options -> mods and toggle your mod on

> activating the mod
![](https://i.imgur.com/HFdncw9.jpg)

restart the game

there should now be a white text THIS IS SPARTA in the bottom left of the screen

> THIS IS SPARTA
![](https://i.imgur.com/jeFErvq.jpg)

______________________________________________________

### some links:

pre-beta cobalt modding  
https://docs.google.com/document/d/1M90Lt0TVeBHJE1VTi-oVaFHplhid2eQek_YVohXNiiU

current modding api documetation (not up to date!)  
https://pastebin.com/d22p4fPz

lua tutorial  
https://learnxinyminutes.com/docs/lua/  
(you can ignore everything from "3.1 Metatables and metamethods",
the current modding api doesn't include metatable stuff, io, loadfile etc.)

______________________________________________________

if the game ever crashes because of your mod, you can find the crashlog here:

`C:\Users\USER_NAME\AppData\Roaming\Cobalt\logs`  
`C:\Users\USER_NAME\AppData\Roaming\Cobalt WASD\logs`

______________________________________________________

if your mod is ever so broken that you can't disable through the in game options, you can disable your mod through this config file

`C:\Users\Familie\AppData\Roaming\Cobalt WASD\mods.sfg`

(in the line `["1234567890_your_mod_id"] = true` change `true` to `false`)

______________________________________________________

if the game ever freezes in fullscreen mode and you are in windows 10 and you can't close the game (even with alt+F4) or even switch to another tab, try this crazy thing (works for me :D)

press ctrl+shift+escape (or press ctrl+alt+delete and click "Task-Manager")  
if you made the task manager the top-most window and it appears, close the game through task manager

if it doesn't appear:  
press win+tab  
drag the frozen window into a new desktop (bottom right)  
now, you should be able to see other windows again and can close the game through task manager

if that doesn't work, i don't even know, just restart your pc :/

______________________________________________________

if you want to quickly iterate on modding without doing [cobalt editor -> mod editor -> workshop -> upload] every time you make a change to your code, you can edit the workshop files directly in:

`C:\Program Files (x86)\Steam\steamapps\workshop\content\357340\WORKSHOP_ID`   (Cobalt)  
`C:\Program Files (x86)\Steam\steamapps\workshop\content\590720\WORKSHOP_ID`   (WASD)

and then just copy the files from there into the documents\cobalt\mods_dev folder and upload the mod when you want to share it with other

______________________________________________________

### now  
### some basics on the modding itself

______________________________________________________

you define functions in your init.lua
and add them to a hook into the game

```lua
local function init ()

end
```
defines a function called init (you can call it anything you want)

and 
```lua
hook.add("modInit", init)
```
adds that function to the modInit hook

`hook` is a global table in the modding api  
`add` is a value (a function) in that table  
`"modInit"` is the name of the hook  
`init` is the function defined by the mod

______________________________________________________

these are the hooks i know of and what arguments they receive (as far as i know):

`"frameUpdate"`  
time delta of current frame (~0.016 for 60 FPS)

`"frameRender"`  
[nothing]

`"mouseButton"`  
mouse x  
mouse y  
mouse button (left = 0, right = 1, middle = 2, scroll-up = 10, scroll-down = 11)

`"windowResized"`  
new window width  
new window height

`"modInit"`  
[nothing]

`"modClose"`  
[nothing]

`"keyPress"`  
keycode of pressed key

`"keyCharacter"`  
keycode of pressed character

______________________________________________________

in the link of the modding api i pasted above  
https://pastebin.com/d22p4fPz

(also, here's the same file as in the pastebin)  
https://github.com/Hobey/Cobalt-modding/blob/master/cobalt_mod_api.lua

you can find an out-of-date list of all the global tables and functions in the modding api  
the lua standard library stuff that the current modding api includes:

functions:
```lua
ipairs()
next()
pairs()
pcall()
tonumber()
tostring()
type()
unpack()
assert()
print()
```

tables (mostly of more functions):
```lua
coroutine
math
os
string
table
```

(the tables do not include all the stuff that the standard library tables include)  
for these you can look up what they do and how you can use them through googling  
(you can also search for them in https://www.lua.org/manual/5.1/#index)

small note:
```lua
assert()
print()
```
work the same as the standard library functions with that name but they are actually custom functions from the game (like, `print` doesn't actually print to a console but directly to the game, and `assert` just prints everything from the second argument and after in redif the assertion fails (= when the first argument is `false` or `nil`))

______________________________________________________

for all other functions, if you want to know what arguments they take, you can either look at the modding api docs or, if the docs don't include the function or if they don't show the arguments for the functions, just pass nothing and look at the local variables in the crash that happens

for example:

for the function renderCircle in the table video the docs only say
```lua
video = {
    -- ...
    renderCircle()
    -- ...
}
```

i can try this
```lua
local function render ()
   video.renderCircle()
end

hook.add("frameRender", render)
```
and i will get a crash
```
cobalt/daisyMoon/lib/renderHelpers.lua:396: attempt to perform arithmetic on local 'radius' (a nil value)
stack traceback:
  [1] cobalt/daisyMoon/lib/renderHelpers.lua:396: in function renderCircle
  [2] ...teamapps/workshop/content/590720/1153371944/init.lua.lua:1933: in function v
  [3] cobalt/daisyMoon/mods.lua:2490: in function func
  [4] lua/hook_interface.lua:26: in function call
  [5] lua/boot.lua:1467: in function render
  [6] lua/boot.lua:1544:in function <lua/boot.lua:1541>

local_variables:
  [1] x = nil, y = nil, radius = nil, alpha = nil, r = nil, g = nil, b = nil
  [3] i = 1, v = [function]
  [4] name = frameRender, hooks = table: 0FEEAC80, _ = 2, func = [function]

...
```

```
  [2] ...teamapps/workshop/content/590720/1153371944/init.lua.lua:1933: in function v
```
corresponds to the line
```lua
   video.renderCircle()
```

```
  [1] cobalt/daisyMoon/lib/renderHelpers.lua:396: in function renderCircle
```
corresponds to the line in the actual function `renderCircle` (which we don't have the source code for) that crashed the game

we can see in
```
local_variables:
  [1] x = nil, y = nil, radius = nil, alpha = nil, r = nil, g = nil, b = nil
```
what arguments the function should have gotten

so now i change the code to
```lua
local function render ()
   --                 x,   y,   radius, alpha, r,   g,   b
   video.renderCircle(100, 200, 50,     127,   255, 255, 127)
end

hook.add("frameRender", render)
```

and i get this dirty yellow circle!

>
![](https://i.imgur.com/WuMwGg7.jpg)

note that not all local variables listed in the crash have to be arguments to the function, but if the function takes any arguments, they will be at the start of the local variables (also, if you ever see `arg`, that means the function takes varargs (any number of arguments))

______________________________________________________

imgur album of used images: https://imgur.com/a/CNuZX
