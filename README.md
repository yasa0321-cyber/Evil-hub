local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local TextChatService = game:GetService("TextChatService")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local lp = Players.LocalPlayer

-- OWNER TAG
do
    local Owners = { ["supremekida2"] = true, ["LLzen0"] = true }
    local tagged = {}
    local function CreateOwnerTag(p)
        if tagged[p] then return end
        local char = p.Character; if not char then return end
        local head = char:FindFirstChild("Head"); if not head then return end
        if char:FindFirstChild("drAk_OTag") then return end
        tagged[p] = true
        local bb = Instance.new("BillboardGui")
        bb.Name = "drAk_OTag"; bb.Size = UDim2.new(0,120,0,26)
        bb.StudsOffset = Vector3.new(0,2.5,0); bb.AlwaysOnTop = true; bb.Parent = char
        local fr = Instance.new("Frame"); fr.Size = UDim2.new(1,0,1,0)
        fr.BackgroundColor3 = Color3.fromRGB(0,0,0); fr.BorderSizePixel = 0; fr.Parent = bb
        Instance.new("UICorner", fr).CornerRadius = UDim.new(0,16)
        local st = Instance.new("UIStroke", fr); st.Color = Color3.fromRGB(255,255,255)
        st.Thickness = 1.5; st.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        local lbl = Instance.new("TextLabel", fr); lbl.Size = UDim2.new(1,0,1,0)
        lbl.BackgroundTransparency = 1; lbl.Font = Enum.Font.GothamBold; lbl.TextSize = 10
        lbl.TextColor3 = Color3.fromRGB(255,215,0); lbl.Text = "drΛk ᴏᴡɴᴇʀ 👑"
        p.CharacterRemoving:Connect(function() tagged[p] = nil end)
    end
    local function check(p)
        if not Owners[p.Name] then return end
        if p.Character then CreateOwnerTag(p) end
        p.CharacterAdded:Connect(function() task.wait(0.5); CreateOwnerTag(p) end)
    end
    for _, p in ipairs(Players:GetPlayers()) do check(p) end
    Players.PlayerAdded:Connect(check)
end



local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "drΛkDuel"
ScreenGui.Parent = CoreGui
ScreenGui.ResetOnSpawn = false

-- Color Palette
local COLORS = {
    MainBG = Color3.fromRGB(6, 6, 8),
    TabBG = Color3.fromRGB(8, 8, 10),
    Border = Color3.fromRGB(220, 220, 220),
    TextActive = Color3.fromRGB(255, 255, 255),
    TextInactive = Color3.fromRGB(48, 48, 48),
    RowBG = Color3.fromRGB(10, 10, 12)
}

-- Nine Hub style colors for oval buttons
local NH = {
    BG      = Color3.fromRGB(8, 8, 8),
    ACTIVE  = Color3.fromRGB(30, 30, 30),
    BORDER  = Color3.fromRGB(50, 50, 50),
    TEXT    = Color3.fromRGB(210, 210, 210),
    DOT_OFF = Color3.fromRGB(50, 50, 50),
    DOT_ON  = Color3.fromRGB(100, 220, 100),
}

-------------------------------------------------------------------------
-- UI CORE ENGINE
-------------------------------------------------------------------------
local function create(class, props)
    local obj = Instance.new(class)
    for k, v in pairs(props) do if k ~= "Parent" then obj[k] = v end end
    obj.Parent = props.Parent
    return obj
end

local ToggleIcon = create("TextButton", {
    Size = UDim2.new(0, 70, 0, 36), Position = UDim2.new(0.05, 0, 0.2, 0),
    BackgroundColor3 = COLORS.MainBG, Text = "drΛk", TextColor3 = COLORS.Border,
    Font = "GothamBold", TextSize = 14, Parent = ScreenGui
})
create("UICorner", {CornerRadius = UDim.new(0, 8), Parent = ToggleIcon})
create("UIStroke", {Color = COLORS.Border, Thickness = 1.5, Parent = ToggleIcon})

local MainFrame = create("Frame", {
    Size = UDim2.new(0, 340, 0, 380), Position = UDim2.new(0.5, -170, 0.5, -190),
    BackgroundColor3 = COLORS.MainBG, BackgroundTransparency = 0.2, Visible = false, Parent = ScreenGui
})
create("UICorner", {CornerRadius = UDim.new(0, 12), Parent = MainFrame})
create("UIStroke", {Color = COLORS.Border, Thickness = 2, Parent = MainFrame})

local Header = create("Frame", {Size = UDim2.new(1, 0, 0, 45), BackgroundTransparency = 1, Parent = MainFrame})
create("TextLabel", {
    Text = "drΛk ᴅᴜᴇʟ", TextColor3 = COLORS.Border, Font = "GothamBold", TextSize = 18,
    Size = UDim2.new(0.5, 0, 1, 0), Position = UDim2.new(0.05, 0, 0, 0),
    BackgroundTransparency = 1, TextXAlignment = "Left", Parent = Header
})

local TabContainer = create("Frame", {
    Size = UDim2.new(0.92, 0, 0, 30), Position = UDim2.new(0.04, 0, 0.13, 0),
    BackgroundColor3 = COLORS.TabBG, Parent = MainFrame
})
create("UICorner", {CornerRadius = UDim.new(0, 6), Parent = TabContainer})
local TabList = create("UIListLayout", {FillDirection = "Horizontal", HorizontalAlignment = "Center", Parent = TabContainer})

local PageContainer = create("Frame", {
    Size = UDim2.new(1, 0, 0.75, 0), Position = UDim2.new(0, 0, 0.22, 0),
    BackgroundTransparency = 1, Parent = MainFrame
})

local Pages = {}
local TabButtons = {}

local function CreatePage(name)
    local Page = create("ScrollingFrame", {
        Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1, Visible = false,
        ScrollBarThickness = 0, AutomaticCanvasSize = "Y", Parent = PageContainer
    })
    create("UIListLayout", {HorizontalAlignment = "Center", Padding = UDim.new(0, 8), Parent = Page})
    Pages[name] = Page
    local TabBtn = create("TextButton", {
        Size = UDim2.new(0.25, 0, 1, 0), BackgroundTransparency = 1,
        Text = name, TextColor3 = COLORS.TextInactive, Font = "GothamBold", TextSize = 10,
        Parent = TabContainer
    })
    TabBtn.MouseButton1Click:Connect(function()
        for _, p in pairs(Pages) do p.Visible = false end
        for _, b in pairs(TabButtons) do b.TextColor3 = COLORS.TextInactive end
        Page.Visible = true
        TabBtn.TextColor3 = COLORS.TextActive
    end)
    TabButtons[name] = TabBtn
end

local ToggleSetters = {}
local ToggleVisuals = {}

local function AddToggle(pageName, labelText, default, callback)
    local active = default
    local Row = create("Frame", {
        Size = UDim2.new(0.92, 0, 0, 50), BackgroundColor3 = COLORS.RowBG,
        BackgroundTransparency = 0.5, Parent = Pages[pageName]
    })
    create("UICorner", {CornerRadius = UDim.new(0, 8), Parent = Row})
    create("UIStroke", {Color = COLORS.Border, Thickness = 1, Transparency = 0.8, Parent = Row})
    create("TextLabel", {
        Text = labelText, Size = UDim2.new(0.6, 0, 1, 0), Position = UDim2.new(0.05, 0, 0, 0),
        TextColor3 = Color3.new(1,1,1), Font = "GothamBold", TextSize = 13,
        BackgroundTransparency = 1, TextXAlignment = "Left", Parent = Row
    })
    local TglBg = create("Frame", {
        Size = UDim2.new(0, 42, 0, 20), Position = UDim2.new(0.95, -42, 0.5, -10),
        BackgroundColor3 = active and Color3.fromRGB(220, 220, 220) or Color3.fromRGB(18, 18, 20), Parent = Row
    })
    create("UICorner", {CornerRadius = UDim.new(1, 0), Parent = TglBg})
    local Circle = create("Frame", {
        Size = UDim2.new(0, 16, 0, 16), Position = active and UDim2.new(0.55, 0, 0.1, 0) or UDim2.new(0.1, 0, 0.1, 0),
        BackgroundColor3 = Color3.new(1, 1, 1), Parent = TglBg
    })
    create("UICorner", {CornerRadius = UDim.new(1, 0), Parent = Circle})
    local Btn = create("TextButton", {Size = UDim2.new(1,0,1,0), BackgroundTransparency = 1, Text = "", Parent = Row})
    local function updateVisual(val)
        active = val
        TweenService:Create(TglBg, TweenInfo.new(0.2), {BackgroundColor3 = val and Color3.fromRGB(220, 220, 220) or Color3.fromRGB(18, 18, 20)}):Play()
        TweenService:Create(Circle, TweenInfo.new(0.2), {Position = val and UDim2.new(0.55, 0, 0.1, 0) or UDim2.new(0.1, 0, 0.1, 0)}):Play()
    end
    local function setState(val)
        if active == val then return end
        updateVisual(val)
        callback(val)
    end
    ToggleSetters[labelText] = setState
    ToggleVisuals[labelText] = updateVisual
    Btn.MouseButton1Click:Connect(function()
        setState(not active)
    end)
end

for _, tab in ipairs({"Combat", "Protect", "Visual", "Settings"}) do CreatePage(tab) end

local function AddButton(pageName, labelText, callback)
    local Row = create("Frame", {
        Size = UDim2.new(0.92, 0, 0, 50), BackgroundColor3 = COLORS.RowBG,
        BackgroundTransparency = 0.5, Parent = Pages[pageName]
    })
    create("UICorner", {CornerRadius = UDim.new(0, 8), Parent = Row})
    create("UIStroke", {Color = COLORS.Border, Thickness = 1, Transparency = 0.8, Parent = Row})
    create("TextLabel", {
        Text = labelText, Size = UDim2.new(0.7, 0, 1, 0), Position = UDim2.new(0.05, 0, 0, 0),
        TextColor3 = Color3.new(1,1,1), Font = "GothamBold", TextSize = 13,
        BackgroundTransparency = 1, TextXAlignment = "Left", Parent = Row
    })
    local arrow = create("TextLabel", {
        Text = "▶", Size = UDim2.new(0, 24, 0, 24), Position = UDim2.new(1, -34, 0.5, -12),
        TextColor3 = Color3.fromRGB(180, 180, 200), Font = "GothamBold", TextSize = 14,
        BackgroundTransparency = 1, Parent = Row
    })
    local Btn = create("TextButton", {Size = UDim2.new(1,0,1,0), BackgroundTransparency = 1, Text = "", Parent = Row})
    Btn.MouseButton1Click:Connect(function() callback() end)
end

local function AddInput(pageName, labelText, defaultVal, suffix, callback)
    suffix = suffix or ""
    local Row = create("Frame", {
        Size = UDim2.new(0.92, 0, 0, 50), BackgroundColor3 = COLORS.RowBG,
        BackgroundTransparency = 0.5, Parent = Pages[pageName]
    })
    create("UICorner", {CornerRadius = UDim.new(0, 8), Parent = Row})
    create("UIStroke", {Color = COLORS.Border, Thickness = 1, Transparency = 0.8, Parent = Row})
    create("TextLabel", {
        Text = labelText, Size = UDim2.new(0.55, 0, 1, 0), Position = UDim2.new(0.05, 0, 0, 0),
        TextColor3 = Color3.new(1,1,1), Font = "GothamBold", TextSize = 12,
        BackgroundTransparency = 1, TextXAlignment = "Left", Parent = Row
    })
    local stroke = create("UIStroke", {Color = Color3.fromRGB(32, 32, 32), Thickness = 1.5})
    local box = create("TextBox", {
        Size = UDim2.new(0.3, 0, 0, 28), Position = UDim2.new(0.95, -74, 0.5, -14),
        BackgroundColor3 = Color3.fromRGB(5, 5, 5),
        Text = tostring(defaultVal) .. suffix,
        TextColor3 = Color3.new(1,1,1), Font = "GothamBold", TextSize = 13,
        ClearTextOnFocus = true, Parent = Row
    })
    create("UICorner", {CornerRadius = UDim.new(0, 6), Parent = box})
    stroke.Parent = box
    box.Focused:Connect(function() stroke.Color = Color3.fromRGB(220,220,220) end)
    box.FocusLost:Connect(function()
        stroke.Color = Color3.fromRGB(32, 32, 32)
        local n = tonumber(box.Text:gsub("[^%d%.]",""))
        if n then
            callback(n)
            box.Text = tostring(n) .. suffix
        else
            box.Text = tostring(defaultVal) .. suffix
        end
    end)
end

TabButtons["Combat"].TextColor3 = COLORS.TextActive
Pages["Combat"].Visible = true

-------------------------------------------------------------------------
-- TIGY VELOCITY HELPER
-------------------------------------------------------------------------
local function tigyApplyVelocity(root, moveDir, speed)
    if not root then return end
    local flat = Vector3.new(moveDir.X, 0, moveDir.Z)
    if flat.Magnitude > 0.05 then
        flat = flat.Unit
        root.AssemblyLinearVelocity = Vector3.new(flat.X * speed, root.AssemblyLinearVelocity.Y, flat.Z * speed)
    else
        local v = root.AssemblyLinearVelocity
        root.AssemblyLinearVelocity = Vector3.new(v.X * 0.82, v.Y, v.Z * 0.82)
    end
end

-------------------------------------------------------------------------
-- SPEED MODULE
-------------------------------------------------------------------------
local Speed = {
    Enabled    = false,
    SpeedValue = 57.9,
    StealValue = 29.9,
    JumpValue  = 45,
    HeartbeatConn = nil,
    JumpConn      = nil,
    _charConn     = nil,
    SpeedUI        = nil,
}

local function createSpeedUI()
    local SpeedScreenGui = create("ScreenGui", {
        Name = "NimbusSpeed", ResetOnSpawn = false, Parent = CoreGui
    })
    local MainFrameS = create("Frame", {
        Name = "SpeedMainFrame",
        Size = UDim2.new(0, 240, 0, 220),
        Position = UDim2.new(1, -250, 0, 325),
        BackgroundColor3 = Color3.fromRGB(8, 8, 10),
        BackgroundTransparency = 0.2,
        BorderSizePixel = 0, Active = true, Draggable = false,
        Parent = SpeedScreenGui
    })
    create("UICorner", {CornerRadius = UDim.new(0, 10), Parent = MainFrameS})
    create("UIStroke", {Color = Color3.fromRGB(255, 255, 255), Thickness = 2, Parent = MainFrameS})
    create("TextLabel", {
        Size = UDim2.new(1, 0, 0, 30), Position = UDim2.new(0, 12, 0, 10),
        BackgroundTransparency = 1, Text = "drΛk Speed Customizer",
        TextColor3 = Color3.fromRGB(220, 220, 220), TextSize = 14,
        TextXAlignment = Enum.TextXAlignment.Left,
        Font = Enum.Font.GothamBold, Parent = MainFrameS
    })
    local Header = create("Frame", {
        Size = UDim2.new(1, -20, 0, 40), Position = UDim2.new(0, 10, 0, 50),
        BackgroundColor3 = Color3.fromRGB(28, 28, 28), BorderSizePixel = 0,
        Parent = MainFrameS
    })
    create("UICorner", {CornerRadius = UDim.new(0, 6), Parent = Header})
    local ToggleBtn = create("TextButton", {
        Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1,
        Text = "ON", TextColor3 = Color3.fromRGB(255, 255, 255), TextSize = 18,
        Font = Enum.Font.GothamBold, Parent = Header
    })
    local function createInputRow(labelText, defaultValue, pos)
        create("TextLabel", {
            Size = UDim2.new(0.6, 0, 0, 30), Position = UDim2.new(0, 12, 0, pos),
            BackgroundTransparency = 1, Text = labelText,
            TextColor3 = Color3.fromRGB(200, 200, 200), TextSize = 14,
            TextXAlignment = Enum.TextXAlignment.Left,
            Font = Enum.Font.Gotham, Parent = MainFrameS
        })
        local box = create("TextBox", {
            Size = UDim2.new(0, 80, 0, 30), Position = UDim2.new(1, -90, 0, pos),
            BackgroundColor3 = Color3.fromRGB(10, 10, 12),
            Text = tostring(defaultValue), TextColor3 = Color3.fromRGB(255, 255, 255),
            Font = Enum.Font.GothamBold, ClearTextOnFocus = true, Parent = MainFrameS
        })
        create("UICorner", {CornerRadius = UDim.new(0, 4), Parent = box})
        return box
    end
    local SpeedInput = createInputRow("Speed",    Speed.SpeedValue, 100)
    local StealInput = createInputRow("Steal Spd",Speed.StealValue, 140)
    local JumpInput  = createInputRow("Jump",     Speed.JumpValue,  180)

    ToggleBtn.MouseButton1Click:Connect(function()
        if Speed.Enabled then
            Speed.Enabled = false
            if Speed.HeartbeatConn then Speed.HeartbeatConn:Disconnect(); Speed.HeartbeatConn = nil end
            ToggleBtn.Text = "OFF"
            Header.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
        else
            Speed.Enabled = true
            if Speed.HeartbeatConn then Speed.HeartbeatConn:Disconnect() end
            Speed.HeartbeatConn = RunService.Heartbeat:Connect(function()
                if not Speed.Enabled then return end
                local char = lp.Character
                local hum  = char and char:FindFirstChildOfClass("Humanoid")
                local root = char and char:FindFirstChild("HumanoidRootPart")
                if not hum or not root then return end
                local isSteal = hum.WalkSpeed < 25
                local targetSpd = isSteal and Speed.StealValue or Speed.SpeedValue
                tigyApplyVelocity(root, hum.MoveDirection, targetSpd)
            end)
            ToggleBtn.Text = "ON"
            Header.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
        end
    end)

    local function validateInput(box, field, min, max)
        box.FocusLost:Connect(function()
            local num = tonumber(box.Text)
            if num then
                num = math.clamp(num, min, max)
                Speed[field] = num
                box.Text = string.format("%.2f", num):gsub("%.?0+$", "")
            else
                box.Text = string.format("%.2f", Speed[field]):gsub("%.?0+$", "")
            end
        end)
    end
    validateInput(SpeedInput, "SpeedValue", 1, 200)
    validateInput(StealInput, "StealValue", 1, 200)
    validateInput(JumpInput,  "JumpValue",  1, 200)

    -- Autosave: write speed values whenever a box loses focus
    local function saveSpeedConfig()
        pcall(function()
            writefile("drAkSpeed.txt",
                tostring(Speed.SpeedValue) .. "," ..
                tostring(Speed.StealValue) .. "," ..
                tostring(Speed.JumpValue)
            )
        end)
    end
    SpeedInput.FocusLost:Connect(saveSpeedConfig)
    StealInput.FocusLost:Connect(saveSpeedConfig)
    JumpInput.FocusLost:Connect(saveSpeedConfig)

    -- =====================================================================
    -- MOBILE-FRIENDLY DRAG (works on iPad, iPhone, PC)
    -- =====================================================================
    local dragSpeed = false
    local startPos = nil
    local startMousePos = nil

    local function onSpeedDragBegan(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragSpeed = true
            startPos = MainFrameS.Position
            startMousePos = Vector2.new(input.Position.X, input.Position.Y)
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragSpeed = false
                end
            end)
        end
    end

    local function onSpeedDragChanged(input)
        if not dragSpeed then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            local delta = Vector2.new(
                input.Position.X - startMousePos.X,
                input.Position.Y - startMousePos.Y
            )
            MainFrameS.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end

    local function onSpeedDragEnded(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragSpeed = false
        end
    end

    MainFrameS.InputBegan:Connect(onSpeedDragBegan)
    MainFrameS.InputEnded:Connect(onSpeedDragEnded)
    UserInputService.InputChanged:Connect(onSpeedDragChanged)
    -- =====================================================================

    return {
        ScreenGui  = SpeedScreenGui,
        ToggleBtn  = ToggleBtn,
        SpeedInput = SpeedInput,
        StealInput = StealInput,
        JumpInput  = JumpInput,
    }
end

local function enableSpeedCustomizer()
    if Speed.Enabled then return end

    if not Speed.SpeedUI then
        Speed.SpeedUI = createSpeedUI()
        Speed.SpeedValue = 57.9
        Speed.StealValue = 29.9
        Speed.JumpValue  = 45
        Speed.SpeedUI.SpeedInput.Text = "57.9"
        Speed.SpeedUI.StealInput.Text = "29.9"
        Speed.SpeedUI.JumpInput.Text  = "45"
    else
        Speed.SpeedUI.ScreenGui.Enabled = true
    end

    Speed.Enabled = true
    Speed.SpeedUI.ToggleBtn.Text = "ON"

    if not Speed._charConn then
        Speed._charConn = lp.CharacterAdded:Connect(function() end)
    end

    Speed.HeartbeatConn = RunService.Heartbeat:Connect(function()
        if not Speed.Enabled then return end
        local char = lp.Character
        local hum  = char and char:FindFirstChildOfClass("Humanoid")
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not hum or not root then return end
        local isSteal = hum.WalkSpeed < 25
        local targetSpd = isSteal and Speed.StealValue or Speed.SpeedValue
        tigyApplyVelocity(root, hum.MoveDirection, targetSpd)
    end)
end

local function enableSpeedSilent()
    if Speed.Enabled then return end
    Speed.SpeedValue = 57.9
    Speed.StealValue = 29.9
    Speed.JumpValue  = 45
    Speed.Enabled = true
    if not Speed._charConn then
        Speed._charConn = lp.CharacterAdded:Connect(function() end)
    end
    Speed.HeartbeatConn = RunService.Heartbeat:Connect(function()
        if not Speed.Enabled then return end
        local char = lp.Character
        local hum  = char and char:FindFirstChildOfClass("Humanoid")
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not hum or not root then return end
        local isSteal = hum.WalkSpeed < 25
        local targetSpd = isSteal and Speed.StealValue or Speed.SpeedValue
        tigyApplyVelocity(root, hum.MoveDirection, targetSpd)
    end)
end

local function showSpeedGuiOff()
    if not Speed.SpeedUI then
        Speed.SpeedUI = createSpeedUI()
    end
    Speed.SpeedUI.ScreenGui.Enabled = true
    Speed.SpeedUI.ToggleBtn.Text = "OFF"
    Speed.SpeedUI.SpeedInput.Text = tostring(Speed.SpeedValue)
    Speed.SpeedUI.StealInput.Text = tostring(Speed.StealValue)
    Speed.SpeedUI.JumpInput.Text  = tostring(Speed.JumpValue)
end

local function disableSpeedCustomizer()
    if not Speed.Enabled then return end
    Speed.Enabled = false
    if Speed.SpeedUI then
        Speed.SpeedUI.ToggleBtn.Text = "OFF"
        Speed.SpeedUI.ScreenGui.Enabled = false
    end
    if Speed._charConn  then Speed._charConn:Disconnect();  Speed._charConn  = nil end
    if Speed.HeartbeatConn then Speed.HeartbeatConn:Disconnect(); Speed.HeartbeatConn = nil end
    if Speed.JumpConn   then Speed.JumpConn:Disconnect();   Speed.JumpConn   = nil end
end

local SpeedCustomizer = Speed

-- Autosave: load saved speed values on startup
do
    local ok, data = pcall(readfile, "drAkSpeed.txt")
    if ok and data and data ~= "" then
        local parts = {}
        for v in data:gmatch("[^,]+") do table.insert(parts, tonumber(v)) end
        if parts[1] then Speed.SpeedValue = parts[1] end
        if parts[2] then Speed.StealValue = parts[2] end
        if parts[3] then Speed.JumpValue  = parts[3] end
    end
end

-------------------------------------------------------------------------
-- SAFE FOLLOW MODULE (Lock Target)
-------------------------------------------------------------------------
local SafeFollow = {
    Enabled = false, Locked = false, FOLLOW_DISTANCE = 6,
    lastMove = 0, gui = nil, btn = nil,
    targetChar = nil, heartbeatConn = nil, charAddedConn = nil
}
local function createSafeFollowUI()
    SafeFollow.gui = Instance.new("ScreenGui"); SafeFollow.gui.Name = "NimbusFollow"
    SafeFollow.gui.ResetOnSpawn = false; SafeFollow.gui.Enabled = false; SafeFollow.gui.Parent = CoreGui
    SafeFollow.btn = Instance.new("TextButton")
    SafeFollow.btn.Size = UDim2.new(0, 120, 0, 40); SafeFollow.btn.Position = UDim2.new(0.5, -60, 0.8, 0)
    SafeFollow.btn.Text = "LOCK: OFF"; SafeFollow.btn.Font = Enum.Font.GothamBold
    SafeFollow.btn.TextSize = 14; SafeFollow.btn.TextColor3 = Color3.new(1, 1, 1)
    SafeFollow.btn.BackgroundColor3 = Color3.fromRGB(32, 32, 32); SafeFollow.btn.AutoButtonColor = true
    SafeFollow.btn.Parent = SafeFollow.gui
    local uiCorner = Instance.new("UICorner"); uiCorner.CornerRadius = UDim.new(0, 8); uiCorner.Parent = SafeFollow.btn
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        SafeFollow.btn.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    SafeFollow.btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = SafeFollow.btn.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
        end
    end)
    SafeFollow.btn.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
    end)
    UserInputService.InputChanged:Connect(function(input) if input == dragInput and dragging then update(input) end end)
    SafeFollow.btn.MouseButton1Click:Connect(function()
        SafeFollow.Locked = not SafeFollow.Locked
        if SafeFollow.Locked then SafeFollow.btn.Text = "LOCK: ON"; SafeFollow.btn.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
        else SafeFollow.btn.Text = "LOCK: OFF"; SafeFollow.btn.BackgroundColor3 = Color3.fromRGB(32, 32, 32) end
    end)
end
local function getNearestPlayer(character, hrp)
    local nearest, dist = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local d = (p.Character.HumanoidRootPart.Position - hrp.Position).Magnitude
            if d < dist then dist = d; nearest = p.Character end
        end
    end
    return nearest
end
local function startSafeFollow()
    if SafeFollow.heartbeatConn then SafeFollow.heartbeatConn:Disconnect() end
    SafeFollow.heartbeatConn = RunService.Heartbeat:Connect(function()
        if not SafeFollow.Enabled then return end
        local character = lp.Character; if not character then return end
        local hrp = character:FindFirstChild("HumanoidRootPart")
        local hum = character:FindFirstChild("Humanoid")
        if not hrp or not hum or hum.Health <= 0 then return end
        local nearest = getNearestPlayer(character, hrp); if not nearest then return end
        local trgHRP = nearest:FindFirstChild("HumanoidRootPart")
        local trgHum = nearest:FindFirstChild("Humanoid")
        if not trgHRP or not trgHum or trgHum.Health <= 0 then return end
        local dir = Vector3.new(trgHRP.Position.X - hrp.Position.X, 0, trgHRP.Position.Z - hrp.Position.Z)
        if dir.Magnitude > 0 then
            hum:MoveTo(trgHRP.Position)
            if Speed.Enabled then
                tigyApplyVelocity(hrp, dir, Speed.SpeedValue)
            end
        end
    end)
end
local function enableSafeFollow()
    if SafeFollow.Enabled then return end
    SafeFollow.Enabled = true
    if not SafeFollow.gui then createSafeFollowUI() end
    SafeFollow.gui.Enabled = true; SafeFollow.Locked = false
    if SafeFollow.btn then SafeFollow.btn.Text = "LOCK: OFF"; SafeFollow.btn.BackgroundColor3 = Color3.fromRGB(32, 32, 32) end
    startSafeFollow()
    SafeFollow.charAddedConn = lp.CharacterAdded:Connect(function(c)
        local hum = c:WaitForChild("Humanoid", 5); if hum then hum.AutoRotate = true end
    end)
    if lp.Character and lp.Character:FindFirstChild("Humanoid") then lp.Character.Humanoid.AutoRotate = true end
end
local function disableSafeFollow()
    if not SafeFollow.Enabled then return end
    SafeFollow.Enabled = false; SafeFollow.Locked = false
    if SafeFollow.gui then SafeFollow.gui.Enabled = false end
    if SafeFollow.heartbeatConn then SafeFollow.heartbeatConn:Disconnect(); SafeFollow.heartbeatConn = nil end
    if SafeFollow.charAddedConn then SafeFollow.charAddedConn:Disconnect(); SafeFollow.charAddedConn = nil end
    SafeFollow.targetChar = nil
    if lp.Character and lp.Character:FindFirstChild("Humanoid") then lp.Character.Humanoid.AutoRotate = true end
end

-------------------------------------------------------------------------
-- IMPROVED ANTI-RAGDOLL MODULE
-------------------------------------------------------------------------
local AntiRagdoll = {
    Enabled = false, RAGDOLL_SPEED = 16, currentCharacter = nil,
    ragdollRemoteConnection = nil, moveConnection = nil, playerModule = nil, controls = nil
}
pcall(function()
    AntiRagdoll.playerModule = require(lp:WaitForChild("PlayerScripts"):WaitForChild("PlayerModule"))
    AntiRagdoll.controls = AntiRagdoll.playerModule:GetControls()
end)
local function cleanupRagdoll()
    if AntiRagdoll.currentCharacter then
        local root = AntiRagdoll.currentCharacter:FindFirstChild("HumanoidRootPart")
        if root then local anchor = root:FindFirstChild("RagdollAnchor"); if anchor then anchor:Destroy() end end
    end
    if AntiRagdoll.moveConnection then AntiRagdoll.moveConnection:Disconnect(); AntiRagdoll.moveConnection = nil end
end
local function disconnectRemote()
    if AntiRagdoll.ragdollRemoteConnection then AntiRagdoll.ragdollRemoteConnection:Disconnect(); AntiRagdoll.ragdollRemoteConnection = nil end
end
local function setupAntiRagdoll(char)
    AntiRagdoll.currentCharacter = char; cleanupRagdoll(); disconnectRemote()
    local humanoid = char:WaitForChild("Humanoid", 5)
    local root = char:WaitForChild("HumanoidRootPart", 5)
    local head = char:WaitForChild("Head", 5)
    if not (humanoid and root and head) then return end
    local ragdollRemote = ReplicatedStorage:WaitForChild("Packages", 8):WaitForChild("Ragdoll", 5):WaitForChild("Ragdoll", 5)
    if not ragdollRemote or not ragdollRemote:IsA("RemoteEvent") then warn("[Anti-Ragdoll] Could not find Ragdoll remote"); return end
    AntiRagdoll.ragdollRemoteConnection = ragdollRemote.OnClientEvent:Connect(function(arg1, arg2)
        if not AntiRagdoll.Enabled then return end
        if arg1 == "Make" or arg2 == "manualM" then
            humanoid:ChangeState(Enum.HumanoidStateType.Freefall)
            Workspace.CurrentCamera.CameraSubject = head; root.CanCollide = false
            if AntiRagdoll.controls then pcall(AntiRagdoll.controls.Enable, AntiRagdoll.controls) end
            cleanupRagdoll()
            local anchor = Instance.new("BodyPosition"); anchor.Name = "RagdollAnchor"
            anchor.MaxForce = Vector3.new(1e6, 1e6, 1e6); anchor.P = 5000; anchor.D = 1000
            anchor.Position = root.Position; anchor.Parent = root
            AntiRagdoll.moveConnection = RunService.RenderStepped:Connect(function()
                if not AntiRagdoll.Enabled or not anchor or not anchor.Parent then
                    if AntiRagdoll.moveConnection then AntiRagdoll.moveConnection:Disconnect(); AntiRagdoll.moveConnection = nil end
                    return
                end
                local moveDir = humanoid.MoveDirection
                if moveDir.Magnitude > 0 then anchor.Position = root.Position + moveDir.Unit * AntiRagdoll.RAGDOLL_SPEED
                else anchor.Position = root.Position end
            end)
        end
        if arg1 == "Destroy" or arg2 == "manualD" then
            humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
            Workspace.CurrentCamera.CameraSubject = humanoid; root.CanCollide = true
            if AntiRagdoll.controls then pcall(AntiRagdoll.controls.Enable, AntiRagdoll.controls) end
            cleanupRagdoll()
        end
    end)
end
lp.CharacterAdded:Connect(function(char) if AntiRagdoll.Enabled then setupAntiRagdoll(char) end end)
lp.CharacterRemoving:Connect(function() cleanupRagdoll(); disconnectRemote(); AntiRagdoll.currentCharacter = nil end)

-------------------------------------------------------------------------
-- ANTI TURRET
-------------------------------------------------------------------------
local AntiTurret = { Enabled = false, Target = nil, Conn = nil, Dist = 60, Pull = -5 }
local function getWeapon() return lp.Backpack:FindFirstChild("Bat") or (lp.Character and lp.Character:FindFirstChild("Bat")) end
local function findTurret()
    local char = lp.Character; if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj.Name:find("Sentry") and not obj.Name:lower():find("bullet") then
            local ownerId = obj.Name:match("Sentry_(%d+)")
            if ownerId and tonumber(ownerId) == lp.UserId then continue end
            local part = obj:IsA("BasePart") and obj or (obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")))
            if part and (char.HumanoidRootPart.Position - part.Position).Magnitude <= AntiTurret.Dist then return obj end
        end
    end
end

-------------------------------------------------------------------------
-- XRAY BASE MODULE
-------------------------------------------------------------------------
local XrayBase = { Enabled = false, OriginalTransparency = {}, Connections = {}, BaseKeywords = {"base", "claim"} }
local function isPlayerBase(obj)
    if not (obj:IsA("BasePart") or obj:IsA("MeshPart") or obj:IsA("UnionOperation")) then return false end
    local n = obj.Name:lower(); local p = obj.Parent and obj.Parent.Name:lower() or ""
    for _, keyword in ipairs(XrayBase.BaseKeywords) do if n:find(keyword) or p:find(keyword) then return true end end
    return false
end
local function applyTransparency(obj) if isPlayerBase(obj) then if not XrayBase.OriginalTransparency[obj] then XrayBase.OriginalTransparency[obj] = obj.LocalTransparencyModifier end; obj.LocalTransparencyModifier = 0.8 end end
local function restoreTransparency(obj) if XrayBase.OriginalTransparency[obj] then obj.LocalTransparencyModifier = XrayBase.OriginalTransparency[obj]; XrayBase.OriginalTransparency[obj] = nil end end
local function enableXrayBase()
    if XrayBase.Enabled then return end; XrayBase.Enabled = true
    for _, obj in ipairs(Workspace:GetDescendants()) do applyTransparency(obj) end
    XrayBase.Connections.descendantAdded = Workspace.DescendantAdded:Connect(function(obj) applyTransparency(obj) end)
    XrayBase.Connections.characterAdded = lp.CharacterAdded:Connect(function() task.wait(0.5); for _, obj in ipairs(Workspace:GetDescendants()) do applyTransparency(obj) end end)
end
local function disableXrayBase()
    if not XrayBase.Enabled then return end; XrayBase.Enabled = false
    for _, conn in pairs(XrayBase.Connections) do conn:Disconnect() end; XrayBase.Connections = {}
    for obj, original in pairs(XrayBase.OriginalTransparency) do obj.LocalTransparencyModifier = original end; XrayBase.OriginalTransparency = {}
end

-------------------------------------------------------------------------
-- NO ANIMATIONS MODULE
-------------------------------------------------------------------------
local NoAnim = { Enabled = false, CharacterConnections = {} }
local function disableAnims(character)
    local humanoid = character:FindFirstChildOfClass("Humanoid"); if not humanoid then return end
    for _, track in pairs(humanoid:GetPlayingAnimationTracks()) do track:Stop() end
    if NoAnim.CharacterConnections[character] then NoAnim.CharacterConnections[character]:Disconnect() end
    NoAnim.CharacterConnections[character] = humanoid.AnimationPlayed:Connect(function(track) track:Stop() end)
end
local function enableNoAnim()
    if NoAnim.Enabled then return end; NoAnim.Enabled = true
    if lp.Character then disableAnims(lp.Character) end
    NoAnim.CharacterConnections.characterAdded = lp.CharacterAdded:Connect(function(char) disableAnims(char) end)
end
local function disableNoAnim()
    if not NoAnim.Enabled then return end; NoAnim.Enabled = false
    if NoAnim.CharacterConnections.characterAdded then NoAnim.CharacterConnections.characterAdded:Disconnect(); NoAnim.CharacterConnections.characterAdded = nil end
    for character, conn in pairs(NoAnim.CharacterConnections) do if character ~= "characterAdded" then conn:Disconnect(); NoAnim.CharacterConnections[character] = nil end end
    if lp.Character then local humanoid = lp.Character:FindFirstChildOfClass("Humanoid"); if humanoid then if NoAnim.CharacterConnections[lp.Character] then NoAnim.CharacterConnections[lp.Character]:Disconnect(); NoAnim.CharacterConnections[lp.Character] = nil end end end
end

-------------------------------------------------------------------------
-- PLAYER ESP MODULE
-------------------------------------------------------------------------
local PlayerESP = { Enabled = false, ESP_COLOR = Color3.fromRGB(220, 220, 220), Highlights = {}, Billboards = {}, Connections = {} }
local function createHighlight(character)
    local h = Instance.new("Highlight"); h.Name = "NimbusESP"; h.Adornee = character
    h.FillColor = PlayerESP.ESP_COLOR; h.FillTransparency = 0.25; h.OutlineColor = PlayerESP.ESP_COLOR
    h.OutlineTransparency = 0; h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop; h.Parent = CoreGui
    return h
end
local function createBillboard(character, player)
    local hrp = character:WaitForChild("HumanoidRootPart", 5); if not hrp then return nil end
    local bb = Instance.new("BillboardGui"); bb.Name = "NimbusESPName"; bb.Adornee = hrp
    bb.AlwaysOnTop = true; bb.Size = UDim2.new(0, 100, 0, 20); bb.StudsOffsetWorldSpace = Vector3.new(0, 3, 0)
    bb.MaxDistance = 600; bb.Parent = CoreGui
    local bg = Instance.new("Frame", bb); bg.Size = UDim2.new(1, 0, 1, 0); bg.BackgroundColor3 = Color3.fromRGB(0, 0, 0); bg.BackgroundTransparency = 0.4
    Instance.new("UICorner", bg).CornerRadius = UDim.new(0, 6)
    local txt = Instance.new("TextLabel", bg); txt.Size = UDim2.new(1, 0, 1, 0); txt.BackgroundTransparency = 1
    txt.Font = Enum.Font.GothamSemibold; txt.TextSize = 13; txt.TextColor3 = Color3.new(1, 1, 1)
    txt.TextStrokeTransparency = 0.2; txt.Text = player.DisplayName
    return bb
end
local function attachESP(player)
    if player == lp then return end
    local function apply(character)
        if PlayerESP.Highlights[player] then pcall(function() PlayerESP.Highlights[player]:Destroy() end) end
        if PlayerESP.Billboards[player] then pcall(function() PlayerESP.Billboards[player]:Destroy() end) end
        PlayerESP.Highlights[player] = createHighlight(character)
        PlayerESP.Billboards[player] = createBillboard(character, player)
    end
    if player.Character then apply(player.Character) end
    local conn = player.CharacterAdded:Connect(apply); table.insert(PlayerESP.Connections, conn)
end
local function removeESP(player)
    if PlayerESP.Highlights[player] then pcall(function() PlayerESP.Highlights[player]:Destroy() end); PlayerESP.Highlights[player] = nil end
    if PlayerESP.Billboards[player] then pcall(function() PlayerESP.Billboards[player]:Destroy() end); PlayerESP.Billboards[player] = nil end
end
local function enableESP()
    if PlayerESP.Enabled then return end; PlayerESP.Enabled = true
    for _, p in ipairs(Players:GetPlayers()) do attachESP(p) end
    table.insert(PlayerESP.Connections, Players.PlayerAdded:Connect(function(p) attachESP(p) end))
    table.insert(PlayerESP.Connections, Players.PlayerRemoving:Connect(function(p) removeESP(p) end))
end
local function disableESP()
    if not PlayerESP.Enabled then return end; PlayerESP.Enabled = false
    for _, h in pairs(PlayerESP.Highlights) do pcall(function() h:Destroy() end) end
    for _, b in pairs(PlayerESP.Billboards) do pcall(function() b:Destroy() end) end
    for _, c in ipairs(PlayerESP.Connections) do pcall(function() c:Disconnect() end) end
    PlayerESP.Highlights = {}; PlayerESP.Billboards = {}; PlayerESP.Connections = {}
end

-------------------------------------------------------------------------
-- INFINITE JUMP MODULE
-------------------------------------------------------------------------
local InfiniteJump = { Enabled = false, JUMP_POWER = 50, COOLDOWN = 0.15, lastJump = 0, JumpConnection = nil }
local function handleJumpRequest()
    if not InfiniteJump.Enabled then return end
    local character = lp.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")
    if hrp and humanoid then
        if humanoid.FloorMaterial == Enum.Material.Air then
            local now = tick()
            if now - InfiniteJump.lastJump >= InfiniteJump.COOLDOWN then
                InfiniteJump.lastJump = now
                hrp.AssemblyLinearVelocity = Vector3.new(
                    hrp.AssemblyLinearVelocity.X,
                    InfiniteJump.JUMP_POWER,
                    hrp.AssemblyLinearVelocity.Z
                )
            end
        end
    end
end
local function enableInfJump() if InfiniteJump.Enabled then return end; InfiniteJump.Enabled = true; InfiniteJump.JumpConnection = UserInputService.JumpRequest:Connect(handleJumpRequest) end
local function disableInfJump() if not InfiniteJump.Enabled then return end; InfiniteJump.Enabled = false; if InfiniteJump.JumpConnection then InfiniteJump.JumpConnection:Disconnect(); InfiniteJump.JumpConnection = nil end end

-------------------------------------------------------------------------
-- MELEE AIMBOT MODULE
-------------------------------------------------------------------------
local Aimbot = {
    Enabled  = false,
    Running  = false,
    GuiOpen  = false,
    RANGE    = 70,
    SPEED    = 50,
    Conn     = nil,
    Attach   = nil,
    LinVel   = nil,
    Align    = nil,
    gui      = nil,
    btn      = nil,
}

local function getMeleeWeapon(char)
    for _, t in ipairs(char:GetChildren()) do
        if t:IsA("Tool") and t.Name:lower():find("bat") then return t end
    end
    for _, t in ipairs(lp.Backpack:GetChildren()) do
        if t:IsA("Tool") and t.Name:lower():find("bat") then return t end
    end
    return nil
end

local function getNearestEnemy(hrp)
    local nearest, dmin = nil, Aimbot.RANGE
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp and p.Character then
            local eHRP = p.Character:FindFirstChild("HumanoidRootPart")
            local eHum = p.Character:FindFirstChildOfClass("Humanoid")
            if eHRP and eHum and eHum.Health > 0 then
                local d = (eHRP.Position - hrp.Position).Magnitude
                if d < dmin then nearest = eHRP; dmin = d end
            end
        end
    end
    return nearest
end

local function startMeleeLogic()
    if Aimbot.Conn then return end
    local char = lp.Character or lp.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")

    Aimbot.Attach = Instance.new("Attachment", hrp)

    Aimbot.LinVel = Instance.new("LinearVelocity", hrp)
    Aimbot.LinVel.Attachment0    = Aimbot.Attach
    Aimbot.LinVel.MaxForce       = 1e9
    Aimbot.LinVel.ForceLimitMode = Enum.ForceLimitMode.PerAxis
    Aimbot.LinVel.MaxAxesForce   = Vector3.new(1e9, 1e9, 1e9)
    Aimbot.LinVel.RelativeTo     = Enum.ActuatorRelativeTo.World
    Aimbot.LinVel.VectorVelocity = Vector3.zero
    Aimbot.LinVel.Enabled        = false

    Aimbot.Align = Instance.new("AlignOrientation", hrp)
    Aimbot.Align.Attachment0    = Aimbot.Attach
    Aimbot.Align.MaxTorque      = math.huge
    Aimbot.Align.Responsiveness = 200
    Aimbot.Align.Enabled        = false

    local lockedTarget = nil

    Aimbot.Conn = RunService.Heartbeat:Connect(function()
        if not Aimbot.Running then return end
        char = lp.Character; if not char then return end
        hrp  = char:FindFirstChild("HumanoidRootPart")
        hum  = char:FindFirstChildOfClass("Humanoid")
        if not hrp or not hum then return end

        if lockedTarget and lockedTarget.Parent then
            local tHum = lockedTarget.Parent:FindFirstChildOfClass("Humanoid")
            if not tHum or tHum.Health <= 0 then lockedTarget = nil end
        else
            lockedTarget = nil
        end

        if not lockedTarget then
            lockedTarget = getNearestEnemy(hrp)
        end

        if not lockedTarget then
            Aimbot.LinVel.Enabled = false
            Aimbot.Align.Enabled  = false
            hum.AutoRotate = true
            return
        end

        hum.AutoRotate = false

        local targetFront = lockedTarget.Position + lockedTarget.CFrame.LookVector * 0.4

        local dir = targetFront - hrp.Position
        local spd = 58.76

        Aimbot.LinVel.Enabled        = true
        Aimbot.LinVel.MaxAxesForce   = Vector3.new(1e9, 1e9, 1e9)
        Aimbot.LinVel.VectorVelocity = dir.Magnitude > 0.1
            and Vector3.new(dir.Unit.X * spd, dir.Unit.Y * spd, dir.Unit.Z * spd)
            or  Vector3.new(0, 0, 0)

        hum.AutoRotate = false
        local targetFacePos = lockedTarget.Position + lockedTarget.CFrame.LookVector * 2
        local lookDir = Vector3.new(targetFacePos.X - hrp.Position.X, 0, targetFacePos.Z - hrp.Position.Z)
        if lookDir.Magnitude > 0.01 then
            Aimbot.Align.Enabled = true
            Aimbot.Align.CFrame  = CFrame.lookAt(hrp.Position, hrp.Position + lookDir)
        end

        local tool = getMeleeWeapon(char)
        if tool then
            if tool.Parent ~= char then hum:EquipTool(tool) end
            tool:Activate()
            local handle = tool:FindFirstChild("Handle")
            if handle then
                for _, p in ipairs(Players:GetPlayers()) do
                    if p ~= lp and p.Character then
                        local tHRP = p.Character:FindFirstChild("HumanoidRootPart")
                        if tHRP and (tHRP.Position - hrp.Position).Magnitude <= 8 then
                            for _, part in ipairs(p.Character:GetChildren()) do
                                if part:IsA("BasePart") then
                                    pcall(function() firetouchinterest(handle, part, 0) end)
                                    pcall(function() firetouchinterest(handle, part, 1) end)
                                end
                            end
                        end
                    end
                end
            end
        end
    end)
end

local function stopMeleeLogic()
    Aimbot.Running = false
    if Aimbot.Conn   then Aimbot.Conn:Disconnect();  Aimbot.Conn   = nil end
    if Aimbot.LinVel then Aimbot.LinVel:Destroy();   Aimbot.LinVel = nil end
    if Aimbot.Align  then Aimbot.Align:Destroy();    Aimbot.Align  = nil end
    if Aimbot.Attach then Aimbot.Attach:Destroy();   Aimbot.Attach = nil end
    if lp.Character then
        local hum = lp.Character:FindFirstChildOfClass("Humanoid")
        if hum then hum.AutoRotate = true end
    end
end

local function enableMeleeAimbot()
    Aimbot.Running = true
    startMeleeLogic()
end

local function disableMeleeAimbot()
    Aimbot.Running = false
    stopMeleeLogic()
end
-------------------------------------------------------------------------
-- OVAL BUTTON STACK (Nine Hub Style) - Float / Lock Target / Drop Brainrot
-------------------------------------------------------------------------
local OvalGui = Instance.new("ScreenGui")
OvalGui.Name = "NimbusOvalStack"
OvalGui.ResetOnSpawn = false
OvalGui.Enabled = false
OvalGui.Parent = CoreGui

local ovalWasDragged = false

local function attachOvalDrag(btn)
    local dragging, dragStart, startPos = false, nil, nil
    btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            ovalWasDragged = false
            dragStart = Vector2.new(input.Position.X, input.Position.Y)
            startPos = btn.Position
        end
    end)
    btn.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            local delta = Vector2.new(input.Position.X - dragStart.X, input.Position.Y - dragStart.Y)
            if delta.Magnitude > 5 then
                ovalWasDragged = true
                btn.Position = UDim2.new(
                    startPos.X.Scale, startPos.X.Offset + delta.X,
                    startPos.Y.Scale, startPos.Y.Offset + delta.Y
                )
            end
        end
    end)
    btn.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

-- Nine Hub style button creator
local function CreateOvalButton(text)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 165, 0, 37)
    btn.BackgroundColor3 = Color3.fromRGB(8, 8, 8)
    btn.BackgroundTransparency = 0.05
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Text = ""

    local uiCorner = Instance.new("UICorner")
    uiCorner.CornerRadius = UDim.new(0, 9)
    uiCorner.Parent = btn

    local uiStroke = Instance.new("UIStroke")
    uiStroke.Color = NH.BORDER
    uiStroke.Thickness = 1
    uiStroke.Transparency = 0.2
    uiStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    uiStroke.Parent = btn

    local dot = Instance.new("Frame")
    dot.Name = "StatusDot"
    dot.Size = UDim2.new(0, 7, 0, 7)
    dot.Position = UDim2.new(0, 9, 0.5, -3)
    dot.BackgroundColor3 = NH.DOT_OFF
    dot.BorderSizePixel = 0
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    dot.Parent = btn

    local lbl = Instance.new("TextLabel")
    lbl.Name = "Label"
    lbl.Size = UDim2.new(1, -50, 1, 0)
    lbl.Position = UDim2.new(0, 22, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = text
    lbl.TextColor3 = NH.TEXT
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = btn

    local gear = Instance.new("TextLabel")
    gear.Size = UDim2.new(0, 16, 0, 16)
    gear.Position = UDim2.new(1, -22, 0.5, -8)
    gear.BackgroundTransparency = 1
    gear.Text = "⚙"
    gear.TextColor3 = Color3.fromRGB(40, 40, 40)
    gear.Font = Enum.Font.Gotham
    gear.TextSize = 13
    gear.Parent = btn

    btn.Position = UDim2.new(0, 0, 0, 0)
    btn.Parent = OvalGui
    return btn
end

local function setNHButtonState(btn, isOn)
    pcall(function()
        btn.BackgroundColor3 ... (86 KB left)
