-- ABYSS UI LIB (CHEF V2)

local UI = {}

local TweenService = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")

local function Tween(obj, props, t)
    TweenService:Create(obj, TweenInfo.new(t or 0.25), props):Play()
end

function UI:Notify(text)
    local gui = Instance.new("ScreenGui", game.CoreGui)

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.new(0, 250, 0, 50)
    frame.Position = UDim2.new(1, 300, 1, -60)
    frame.BackgroundColor3 = Color3.fromRGB(30,30,30)

    Instance.new("UICorner", frame)

    local txt = Instance.new("TextLabel", frame)
    txt.Size = UDim2.new(1,0,1,0)
    txt.Text = text
    txt.TextColor3 = Color3.new(1,1,1)
    txt.BackgroundTransparency = 1

    Tween(frame, {Position = UDim2.new(1, -260, 1, -60)})
    task.wait(3)
    Tween(frame, {Position = UDim2.new(1, 300, 1, -60)})
    task.wait(0.3)
    gui:Destroy()
end

function UI:CreateWindow(name)
    local ScreenGui = Instance.new("ScreenGui", game.CoreGui)

    local Main = Instance.new("Frame", ScreenGui)
    Main.Size = UDim2.new(0, 520, 0, 330)
    Main.Position = UDim2.new(0.5, -260, 0.5, -165)
    Main.BackgroundColor3 = Color3.fromRGB(20,20,20)

    Instance.new("UICorner", Main)

    local Top = Instance.new("Frame", Main)
    Top.Size = UDim2.new(1,0,0,40)
    Top.BackgroundColor3 = Color3.fromRGB(30,30,30)

    local Title = Instance.new("TextLabel", Top)
    Title.Size = UDim2.new(1,-40,1,0)
    Title.Text = name
    Title.TextColor3 = Color3.new(1,1,1)
    Title.BackgroundTransparency = 1

    local MinBtn = Instance.new("TextButton", Top)
    MinBtn.Size = UDim2.new(0,40,1,0)
    MinBtn.Position = UDim2.new(1,-40,0,0)
    MinBtn.Text = "-"
    MinBtn.BackgroundColor3 = Color3.fromRGB(50,50,50)
    MinBtn.TextColor3 = Color3.new(1,1,1)

    local minimized = false

    MinBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        if minimized then
            Tween(Main, {Size = UDim2.new(0, 520, 0, 40)})
        else
            Tween(Main, {Size = UDim2.new(0, 520, 0, 330)})
        end
    end)

    -- DRAG
    local dragging, dragInput, dragStart, startPos

    Top.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position
        end
    end)

    Top.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            Main.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    local Tabs = Instance.new("Frame", Main)
    Tabs.Size = UDim2.new(0,130,1,-40)
    Tabs.Position = UDim2.new(0,0,0,40)
    Tabs.BackgroundColor3 = Color3.fromRGB(25,25,25)

    Instance.new("UIListLayout", Tabs)

    local Content = Instance.new("Frame", Main)
    Content.Size = UDim2.new(1,-130,1,-40)
    Content.Position = UDim2.new(0,130,0,40)
    Content.BackgroundTransparency = 1

    local Window = {}

    function Window:CreateTab(name)
        local Btn = Instance.new("TextButton", Tabs)
        Btn.Size = UDim2.new(1,0,0,40)
        Btn.Text = name
        Btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
        Btn.TextColor3 = Color3.new(1,1,1)

        local Page = Instance.new("Frame", Content)
        Page.Size = UDim2.new(1,0,1,0)
        Page.Visible = false
        Page.BackgroundTransparency = 1

        local Layout = Instance.new("UIListLayout", Page)
        Layout.Padding = UDim.new(0,5)

        Btn.MouseButton1Click:Connect(function()
            for _,v in pairs(Content:GetChildren()) do
                if v:IsA("Frame") then
                    v.Visible = false
                end
            end
            Page.Visible = true
        end)

        local Tab = {}

        function Tab:CreateButton(text, callback)
            local b = Instance.new("TextButton", Page)
            b.Size = UDim2.new(1,-10,0,40)
            b.Text = text
            b.BackgroundColor3 = Color3.fromRGB(40,40,40)
            b.TextColor3 = Color3.new(1,1,1)

            Instance.new("UICorner", b)

            b.MouseButton1Click:Connect(function()
                Tween(b, {Size = UDim2.new(1,-5,0,38)})
                task.wait(0.1)
                Tween(b, {Size = UDim2.new(1,-10,0,40)})
                callback()
            end)
        end

        function Tab:CreateToggle(text, callback)
            local state = false

            local t = Instance.new("TextButton", Page)
            t.Size = UDim2.new(1,-10,0,40)
            t.Text = text.." [OFF]"
            t.BackgroundColor3 = Color3.fromRGB(40,40,40)
            t.TextColor3 = Color3.new(1,1,1)

            Instance.new("UICorner", t)

            t.MouseButton1Click:Connect(function()
                state = not state
                t.Text = text.." ["..(state and "ON" or "OFF").."]"
                callback(state)
            end)
        end

        function Tab:CreateSlider(text, min, max, callback)
            local value = min

            local s = Instance.new("TextButton", Page)
            s.Size = UDim2.new(1,-10,0,40)
            s.Text = text..": "..value
            s.BackgroundColor3 = Color3.fromRGB(40,40,40)
            s.TextColor3 = Color3.new(1,1,1)

            Instance.new("UICorner", s)

            s.MouseButton1Click:Connect(function()
                value += 1
                if value > max then value = min end
                s.Text = text..": "..value
                callback(value)
            end)
        end

        return Tab
    end

    return Window
end

return UI
