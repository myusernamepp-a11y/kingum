-- Kingum Script V5 (Modified): الطيران + الآيم بوت الذكي + الـ ESP الشامل + الـ Weapon ESP + الـ SafeZone الذكي + زر God Mode (تحت التطوير)
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

-- النطاق القريب المفضل للأيم بوت
local CloseRange = 80 

-- 1. إنشاء العناصر الأساسية للواجهة الرسومية (GUI)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "KingumScriptGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.05, 0, 0.3, 0)
MainFrame.Size = UDim2.new(0, 240, 0, 275)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 10)
UICorner.Parent = MainFrame

local UIGradient = Instance.new("UIGradient")
UIGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 30, 30)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 15, 15))
})
UIGradient.Rotation = 45
UIGradient.Parent = MainFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "TitleLabel"
TitleLabel.BackgroundTransparency = 1
TitleLabel.Position = UDim2.new(0, 10, 0, 10)
TitleLabel.Size = UDim2.new(1, -20, 0, 30)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.Text = "kingum script"
TitleLabel.TextColor3 = Color3.fromRGB(50, 150, 255)
TitleLabel.TextSize = 22
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = MainFrame

-- 2. ميزة كشف الأماكن (ESP) المتطورة + الـ Weapon ESP
local function ApplyESP(character)
    if not character then return end
    local player = Players:GetPlayerFromCharacter(character)
    if player == LocalPlayer then return end
    
    task.defer(function()
        local rootPart = character:WaitForChild("HumanoidRootPart", 5)
        if not rootPart then return end
        
        local oldHighlight = character:FindFirstChild("ESP_Highlight")
        if oldHighlight then oldHighlight:Destroy() end
        
        local Highlight = Instance.new("Highlight")
        Highlight.Name = "ESP_Highlight"
        Highlight.FillColor = Color3.fromRGB(255, 0, 0)
        Highlight.FillTransparency = 0.5
        Highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        Highlight.OutlineTransparency = 0
        Highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        Highlight.Adornee = character
        Highlight.Parent = character

        local oldWeaponGui = character:FindFirstChild("WeaponESP_Gui")
        if oldWeaponGui then oldWeaponGui:Destroy() end

        local BillboardGui = Instance.new("BillboardGui")
        BillboardGui.Name = "WeaponESP_Gui"
        BillboardGui.AlwaysOnTop = true
        BillboardGui.Size = UDim2.new(0, 200, 0, 50)
        BillboardGui.StudsOffset = Vector3.new(0, 3, 0)
        BillboardGui.Adornee = character:FindFirstChild("Head") or rootPart
        BillboardGui.Parent = character

        local WeaponLabel = Instance.new("TextLabel")
        WeaponLabel.Name = "WeaponLabel"
        WeaponLabel.BackgroundTransparency = 1
        WeaponLabel.Size = UDim2.new(1, 0, 1, 0)
        WeaponLabel.Font = Enum.Font.SourceSansBold
        WeaponLabel.TextSize = 14
        WeaponLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
        WeaponLabel.TextStrokeTransparency = 0
        WeaponLabel.Text = "[Checking Weapon...]"
        WeaponLabel.Parent = BillboardGui

        task.spawn(function()
            while character and character.Parent and BillboardGui and BillboardGui.Parent do
                local tool = character:FindFirstChildOfClass("Tool")
                if tool then
                    WeaponLabel.Text = "[" .. tool.Name .. "]"
                    WeaponLabel.TextColor3 = Color3.fromRGB(255, 85, 85)
                else
                    WeaponLabel.Text = "[No Weapon]"
                    WeaponLabel.TextColor3 = Color3.fromRGB(85, 255, 85)
                end
                task.wait(0.5)
            end
        end)
    end)
end

local function MonitorPlayer(player)
    if player == LocalPlayer then return end
    if player.Character then ApplyESP(player.Character) end
    player.CharacterAdded:Connect(ApplyESP)
end

for _, player in pairs(Players:GetPlayers()) do MonitorPlayer(player) end
Players.PlayerAdded:Connect(MonitorPlayer)

task.spawn(function()
    while true do
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                if not player.Character:FindFirstChild("ESP_Highlight") or not player.Character:FindFirstChild("WeaponESP_Gui") then
                    ApplyESP(player.Character)
                end
            end
        end
        task.wait(3)
    end
end)

-- 3. إعدادات وميزات الـ Aimbot الذكي
local Aiming = false
local CurrentTarget = nil

local function IsInSafeZone(player, character)
    if character:FindFirstChildOfClass("ForceField") then return true end
    if character:FindFirstChild("SafeZone") or character:FindFirstChild("InSafeZone") or character:FindFirstChild("Safe") then return true end
    if player:FindFirstChild("SafeZone") or (player:FindFirstChild("States") and player.States:FindFirstChild("SafeZone")) then
        local sz = player:FindFirstChild("SafeZone") or player.States.SafeZone
        if sz.Value == true then return true end
    end
    return false
end

local function IsVisible(targetPlayer, targetCharacter)
    if IsInSafeZone(targetPlayer, targetCharacter) then return false end
    local targetPart = targetCharacter:FindFirstChild("HumanoidRootPart") or targetCharacter:FindFirstChild("Torso")
    if not targetPart then return false end
    
    local origin = Camera.CFrame.Position
    local direction = targetPart.Position - origin
    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character, Camera}
    raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    raycastParams.IgnoreWater = true
    
    local raycastResult = workspace:Raycast(origin, direction, raycastParams)
    if not raycastResult or raycastResult.Instance:IsDescendantOf(targetCharacter) then return true end
    return false
end

local function GetClosestPlayer()
    if CurrentTarget and CurrentTarget.Character and CurrentTarget.Character:FindFirstChildOfClass("Humanoid") and CurrentTarget.Character:FindFirstChildOfClass("Humanoid").Health > 0 and IsVisible(CurrentTarget, CurrentTarget.Character) then
        return CurrentTarget
    end
    
    local ClosestPlayer = nil
    local ShortestDistance = CloseRange
    
    local OverallClosestPlayer = nil
    local OverallShortestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChildOfClass("Humanoid") and player.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
            local TargetPart = player.Character:FindFirstChild("HumanoidRootPart")
            local LocalPart = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            
            if TargetPart and LocalPart and IsVisible(player, player.Character) then
                local Distance = (TargetPart.Position - LocalPart.Position).Magnitude
                local _, OnScreen = Camera:WorldToViewportPoint(TargetPart.Position)
                
                if OnScreen then
                    if Distance < OverallShortestDistance then
                        OverallShortestDistance = Distance
                        OverallClosestPlayer = player
                    end
                    
                    if Distance <= ShortestDistance then
                        ShortestDistance = Distance
                        ClosestPlayer = player
                    end
                end
            end
        end
    end
    
    return ClosestPlayer or OverallClosestPlayer
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.E then
        Aiming = not Aiming
        if not Aiming then CurrentTarget = nil end
    end
end)

-- 4. ميزة Fly المتطورة مع التحكم في السرعة
local Flying = false
local FlySpeed = 100
local MaxFlySpeed = 200
local MinFlySpeed = 20
local BodyGyro = nil
local BodyVelocity = nil

local FlyStatusButton = Instance.new("TextButton")
FlyStatusButton.Name = "FlyStatusButton"
FlyStatusButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
FlyStatusButton.BorderSizePixel = 0
FlyStatusButton.Position = UDim2.new(0.05, 0, 0, 50)
FlyStatusButton.Size = UDim2.new(0.9, 0, 0, 35)
FlyStatusButton.Font = Enum.Font.SourceSans
FlyStatusButton.Text = "FLY: DISABLED"
FlyStatusButton.TextColor3 = Color3.fromRGB(200, 200, 200)
FlyStatusButton.TextSize = 16
FlyStatusButton.AutoButtonColor = true
FlyStatusButton.Parent = MainFrame

local BtnCorner1 = Instance.new("UICorner")
BtnCorner1.CornerRadius = UDim.new(0, 6)
BtnCorner1.Parent = FlyStatusButton

FlyStatusButton.MouseButton1Click:Connect(function()
    Flying = not Flying
    local character = LocalPlayer.Character
    if Flying then
        FlyStatusButton.Text = "FLY: ENABLED"
        FlyStatusButton.BackgroundColor3 = Color3.fromRGB(50, 100, 50)
        FlyStatusButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        FlyStatusButton.Font = Enum.Font.SourceSansBold
        if character and character:FindFirstChild("HumanoidRootPart") then
            BodyGyro = Instance.new("BodyGyro")
            BodyGyro.P = 9e4
            BodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            BodyGyro.cframe = character.HumanoidRootPart.CFrame
            BodyGyro.Parent = character.HumanoidRootPart
            BodyVelocity = Instance.new("BodyVelocity")
            BodyVelocity.velocity = Vector3.new(0, 0, 0)
            BodyVelocity.maxForce = Vector3.new(9e9, 9e9, 9e9)
            BodyVelocity.Parent = character.HumanoidRootPart
            character.Humanoid.PlatformStand = true
        end
    else
        FlyStatusButton.Text = "FLY: DISABLED"
        FlyStatusButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        FlyStatusButton.TextColor3 = Color3.fromRGB(200, 200, 200)
        FlyStatusButton.Font = Enum.Font.SourceSans
        if BodyGyro then BodyGyro:Destroy() end
        if BodyVelocity then BodyVelocity:Destroy() end
        if character and character:FindFirstChild("Humanoid") then character.Humanoid.PlatformStand = false end
    end
end)

-- 5. خيار Teleport Safezone (beta) الذكي
local TeleportSafeZoneActive = false
local IsTeleported = false

local TeleportButton = Instance.new("TextButton")
TeleportButton.Name = "TeleportButton"
TeleportButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
TeleportButton.BorderSizePixel = 0
TeleportButton.Position = UDim2.new(0.05, 0, 0, 92)
TeleportButton.Size = UDim2.new(0.9, 0, 0, 35)
TeleportButton.Font = Enum.Font.SourceSans
TeleportButton.Text = "Teleport Safezone (beta)"
TeleportButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TeleportButton.TextSize = 16
TeleportButton.AutoButtonColor = true
TeleportButton.Parent = MainFrame

local BtnCorner2 = Instance.new("UICorner")
BtnCorner2.CornerRadius = UDim.new(0, 6)
BtnCorner2.Parent = TeleportButton

TeleportButton.MouseButton1Click:Connect(function()
    TeleportSafeZoneActive = not TeleportSafeZoneActive
    IsTeleported = false
    if TeleportSafeZoneActive then
        TeleportButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
    else
        TeleportButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
    end
end)

task.spawn(function()
    while true do
        if TeleportSafeZoneActive then
            local character = LocalPlayer.Character
            if character and character:FindFirstChild("Humanoid") and character:FindFirstChild("HumanoidRootPart") then
                local humanoid = character.Humanoid
                local health = humanoid.Health

                if health > 0 and health <= 50 then
                    if not IsTeleported then
                        character.HumanoidRootPart.CFrame = CFrame.new(120, 127, 665)
                        IsTeleported = true 
                    end
                elseif health > 50 then
                    IsTeleported = false 
                end
            end
        else
            IsTeleported = false
        end
        task.wait(0.2)
    end
end)

-- 6. خيار God Mode (تحت التطوير)
local GodModeButton = Instance.new("TextButton")
GodModeButton.Name = "GodModeButton"
GodModeButton.BackgroundColor3 = Color3.fromRGB(80, 80, 80) -- لون رمادي يدل على القفل
GodModeButton.BorderSizePixel = 0
GodModeButton.Position = UDim2.new(0.05, 0, 0, 134)
GodModeButton.Size = UDim2.new(0.9, 0, 0, 35)
GodModeButton.Font = Enum.Font.SourceSansItalic -- خط مائل ليعطي طابع التطوير
GodModeButton.Text = "God Mode (تحت التطوير)"
GodModeButton.TextColor3 = Color3.fromRGB(180, 180, 180)
GodModeButton.TextSize = 15
GodModeButton.AutoButtonColor = false -- غير قابل للضغط حالياً
GodModeButton.Parent = MainFrame

local BtnCorner3 = Instance.new("UICorner")
BtnCorner3.CornerRadius = UDim.new(0, 6)
BtnCorner3.Parent = GodModeButton

-- 7. إنشاء الـ Slider (المزلاق) للتحكم في سرعة الطيران
local SliderFrame = Instance.new("Frame")
SliderFrame.Name = "SliderFrame"
SliderFrame.BackgroundTransparency = 1
SliderFrame.Position = UDim2.new(0.05, 0, 0, 185)
SliderFrame.Size = UDim2.new(0.9, 0, 0, 70)
SliderFrame.Parent = MainFrame

local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Name = "SpeedLabel"
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Size = UDim2.new(1, 0, 0, 20)
SpeedLabel.Font = Enum.Font.SourceSans
SpeedLabel.Text = "FLY SPEED: " .. FlySpeed
SpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedLabel.TextSize = 16
SpeedLabel.Parent = SliderFrame

local SliderBack = Instance.new("Frame")
SliderBack.Name = "SliderBack"
SliderBack.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
SliderBack.Position = UDim2.new(0, 0, 0, 25)
SliderBack.Size = UDim2.new(1, 0, 0, 10)
SliderBack.BorderSizePixel = 0
SliderBack.Parent = SliderFrame

local SliderKnob = Instance.new("TextButton")
SliderKnob.Name = "SliderKnob"
SliderKnob.BackgroundColor3 = Color3.fromRGB(50, 150, 255)
SliderKnob.BorderSizePixel = 0
SliderKnob.Position = UDim2.new((FlySpeed - MinFlySpeed) / (MaxFlySpeed - MinFlySpeed), -10, 0, -5)
SliderKnob.Size = UDim2.new(0, 20, 0, 20)
SliderKnob.Text = ""
SliderKnob.Parent = SliderBack

local KnobCorner = Instance.new("UICorner")
KnobCorner.CornerRadius = UDim.new(1, 0)
KnobCorner.Parent = SliderKnob

local function UpdateFlySpeed(knobPositionX)
    local percentage = math.clamp(knobPositionX / SliderBack.AbsoluteSize.X, 0, 1)
    FlySpeed = math.round(MinFlySpeed + (percentage * (MaxFlySpeed - MinFlySpeed)))
    SpeedLabel.Text = "FLY SPEED: " .. FlySpeed
end

local DraggingKnob = false
SliderKnob.MouseButton1Down:Connect(function() DraggingKnob = true end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then DraggingKnob = false end
end)

UserInputService.InputChanged:Connect(function(input)
    if DraggingKnob and input.UserInputType == Enum.UserInputType.MouseMovement then
        local mousePositionX = input.Position.X
        local sliderBackPositionX = SliderBack.AbsolutePosition.X
        local knobNewPositionX = mousePositionX - sliderBackPositionX
        UpdateFlySpeed(knobNewPositionX)
        local newPercentage = math.clamp(knobNewPositionX / SliderBack.AbsoluteSize.X, 0, 1)
        SliderKnob.Position = UDim2.new(newPercentage, -10, 0, -5)
    end
end)

-- التحديث المباشر لحركة الطيران وإقفال الأيم بوت
RunService.RenderStepped:Connect(function()
    local character = LocalPlayer.Character
    if Flying and BodyVelocity and BodyGyro and character and character:FindFirstChild("HumanoidRootPart") then
        BodyGyro.cframe = Camera.CFrame
        local velocity = Vector3.new(0, 0, 0)
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then velocity = velocity + (Camera.CFrame.LookVector * FlySpeed) end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then velocity = velocity - (Camera.CFrame.LookVector * FlySpeed) end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then velocity = velocity - (Camera.CFrame.RightVector * FlySpeed) end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then velocity = velocity + (Camera.CFrame.RightVector * FlySpeed) end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then velocity = velocity + (Camera.CFrame.UpVector * FlySpeed) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then velocity = velocity - (Camera.CFrame.UpVector * FlySpeed) end
        BodyVelocity.velocity = velocity
    end
    
    if Aiming then
        CurrentTarget = GetClosestPlayer()
        if CurrentTarget and CurrentTarget.Character then
            local TargetPart = CurrentTarget.Character:FindFirstChild("HumanoidRootPart")
            if TargetPart then
                Camera.CFrame = CFrame.lookAt(Camera.CFrame.Position, TargetPart.Position)
            end
        end
    end
end)

-- 8. زر إخفاء/إظهار الواجهة (RightShift)
local GuiVisible = true
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
        GuiVisible = not GuiVisible
        MainFrame.Visible = GuiVisible
    end
end)
