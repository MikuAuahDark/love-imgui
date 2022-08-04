# LOVE-IMGUI

[imgui](https://github.com/ocornut/imgui) module for the [LÖVE](https://love2d.org/) game engine including lua bindings based on this [project](https://github.com/patrickriordan/imgui_lua_bindings).
**The main difference is that now by default in this version the return values ordering is reverted.** For instance to retrieve the value from a slider, you need to do:
```lua
floatValue, status = imgui.SliderFloat("SliderFloat", floatValue, 0.0, 1.0);
```
Or if you're not interested to know if the field was modified, just:
```lua
floatValue = imgui.SliderFloat("SliderFloat", floatValue, 0.0, 1.0);
```
To reverse this behavior and receive back the return values from a function first before the modified fields, just call at the beginning of your application:
```lua
imgui.SetReturnValueLast(false)
```

Another notable difference is that enum values are handled using strings (and array of strings) instead of numerical values, for instance to create a window:
```lua
imgui.Begin("Test Window", true, { "ImGuiWindowFlags_AlwaysAutoResize", "ImGuiWindowFlags_NoTitleBar" });
```
Or for a single flag:
```lua
imgui.Begin("Test Window", true, "ImGuiWindowFlags_AlwaysAutoResize");
```

It uses imgui 1.73 and is based on LÖVE 11.1.

## Getting Started 

Build the project using CMake:

```
cmake -Bbuild -H. -DCMAKE_INSTALL_PREFIX=$PWD/installdir -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release --target install
```

(Windows users may want to change `$PWD` with `%CD%` when running from cmd)

Then copy the generated dynamic module next to your love executable or into the LÖVE application data folder (for instance "C:/Users/<user>/AppData/Roaming/LOVE" on Windows or ~/.local/shared/love on Linux).

Pre-built binaries for Windows and Mas OSX are provided in the [releases](https://github.com/slages/love-imgui/releases) page.

## Examples

### Simple integration
```lua
local imgui = require "imgui"

local showTestWindow = false
local showAnotherWindow = false
local floatValue = 0;
local sliderFloat = { 0.1, 0.5 }
local clearColor = { 0.2, 0.2, 0.2 }
local comboSelection = 1
local textValue = "text"

--
-- LOVE callbacks
--
function love.load(arg)
end

function love.update(dt)
    imgui.NewFrame()
end

function love.draw()

    -- Menu
    if imgui.BeginMainMenuBar() then
        if imgui.BeginMenu("File") then
            imgui.MenuItem("Test")
            imgui.EndMenu()
        end
        imgui.EndMainMenuBar()
    end

    -- Debug window
    imgui.Text("Hello, world!");
    clearColor[1], clearColor[2], clearColor[3] = imgui.ColorEdit3("Clear color", clearColor[1], clearColor[2], clearColor[3]);
    
    -- Sliders
    floatValue = imgui.SliderFloat("SliderFloat", floatValue, 0.0, 1.0);
    sliderFloat[1], sliderFloat[2] = imgui.SliderFloat2("SliderFloat2", sliderFloat[1], sliderFloat[2], 0.0, 1.0);
    
    -- Combo
    comboSelection = imgui.Combo("Combo", comboSelection, { "combo1", "combo2", "combo3", "combo4" }, 4);

    -- Windows
    if imgui.Button("Test Window") then
        showTestWindow = not showTestWindow;
    end
    
    if imgui.Button("Another Window") then
        showAnotherWindow = not showAnotherWindow;
    end
    
    if showAnotherWindow then
        imgui.SetNextWindowPos(50, 50, "ImGuiCond_FirstUseEver")
        showAnotherWindow = imgui.Begin("Another Window", true, { "ImGuiWindowFlags_AlwaysAutoResize", "ImGuiWindowFlags_NoTitleBar" });
        imgui.Text("Hello");
        -- Input text
        textValue = imgui.InputTextMultiline("InputText", textValue, 200, 300, 200);
        imgui.End();
    end

    if showTestWindow then
        showTestWindow = imgui.ShowDemoWindow(true)
    end

    love.graphics.clear(clearColor[1], clearColor[2], clearColor[3])
    imgui.Render();
end

function love.quit()
    imgui.ShutDown();
end

--
-- User inputs
--
function love.textinput(t)
    imgui.TextInput(t)
    if not imgui.GetWantCaptureKeyboard() then
        -- Pass event to the game
    end
end

function love.keypressed(key)
    imgui.KeyPressed(key)
    if not imgui.GetWantCaptureKeyboard() then
        -- Pass event to the game
    end
end

function love.keyreleased(key)
    imgui.KeyReleased(key)
    if not imgui.GetWantCaptureKeyboard() then
        -- Pass event to the game
    end
end

function love.mousemoved(x, y)
    imgui.MouseMoved(x, y)
    if not imgui.GetWantCaptureMouse() then
        -- Pass event to the game
    end
end

function love.mousepressed(x, y, button)
    imgui.MousePressed(button)
    if not imgui.GetWantCaptureMouse() then
        -- Pass event to the game
    end
end

function love.mousereleased(x, y, button)
    imgui.MouseReleased(button)
    if not imgui.GetWantCaptureMouse() then
        -- Pass event to the game
    end
end

function love.wheelmoved(x, y)
    imgui.WheelMoved(y)
    if not imgui.GetWantCaptureMouse() then
        -- Pass event to the game
    end
end
```
### Fonts

Since `ImGui::IO` isn't exposed to Lua, the `ImFontAtlas::AddFontFileFromTTF` function is forwarded to the main API table, enabling the use of custom fonts:

```Lua
local fontHandle = imgui.AddFontFileFromTTF("pathToFont.tff", 16)
imgui.PushFont(fontHandle)
imgui.Text "Hello"
imgui.PopFont()
```

Note that `imgui.NewFrame()` must be called *at least once* before using `imgui.PushFont()`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

