-- [[ 👑 RXT SERVER - PRIVATE SCRIPT ]] --
-- Features: Ghost Fast TP | Anti-AFK | No Ragdoll | Infinity Jump | Custom Speed

if not game:IsLoaded() then game.Loaded:Wait() end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local Lighting = game:GetService("Lighting")
local player = Players.LocalPlayer

-- [ الإعدادات المتغيرة ]
local stealthSpeedEnabled = false
local speedValue = 50
local noclipEnabled = false
local godModeEnabled = false
local clickTpEnabled = false
local instantInteractionEnabled = false
local infJumpEnabled = false
local noRagdollEnabled = false
local savedPosition = nil

-- [1] نظام مانع الطرد (Anti-AFK)
local VirtualUser = game:GetService("VirtualUser")
player.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

-- [2] نظام مانع السقوط / راقدول (No Ragdoll)
-- يعمل عن طريق منع تغيير حالة الانبطاح أو السقوط
RunService.Stepped:Connect(function()
    if noRagdollEnabled and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
        local hum = player.Character:FindFirstChildOfClass("Humanoid")
        -- منع الحالات التي تسبب سقوط الشخصية
        hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
        hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        hum:SetStateEnabled(Enum.HumanoidStateType.PlatformStanding, false)
    end
end)

-- [3] وظيفة الانتقال الشبح السريع
local function ghostTP(targetCFrame)
    if not player.Character or not targetCFrame then return end
    local root = player.Character.HumanoidRootPart
    local distance = (root.Position - targetCFrame.Position).Magnitude
    local speed = 120 
    local duration = distance / speed
    local startTime = tick()
    local startCFrame = root.CFrame
    
    local connection
    connection = RunService.Heartbeat:Connect(function()
        local elapsed = tick() - startTime
        local fraction = elapsed / duration
        if fraction >= 1 then
            root.CFrame = targetCFrame
            connection:Disconnect()
        else
            root.CFrame = startCFrame:Lerp(targetCFrame, fraction)
            root.Velocity = Vector3.new(0, 0, 0)
        end
    end)
end

-- [4] محركات الحركة
RunService.Heartbeat:Connect(function(delta)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = player.Character.HumanoidRootPart
        if stealthSpeedEnabled then
            local hum = player.Character:FindFirstChildOfClass("Humanoid")
            if hum.MoveDirection.Magnitude > 0 then
                root.CFrame = root.CFrame + (hum.MoveDirection * (speedValue / 8) * delta * 10)
            end
        end
        if noclipEnabled then
            for _, v in pairs(player.Character:GetDescendants()) do
                if v:IsA("BasePart") then v.CanCollide = false end
            end
        end
        if godModeEnabled then player.Character.Humanoid.Health = 100 end
    end
end)

UserInputService.JumpRequest:Connect(function()
    if infJumpEnabled and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
        player.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
    end
end)

-- [5] الواجهة (RXT SERVER)
if CoreGui:FindFirstChild("RXT_Private") then CoreGui["RXT_Private"]:Destroy() end
local ScreenGui = Instance.new("ScreenGui", CoreGui)
ScreenGui.Name = "RXT_Private"

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 350, 0, 520)
Main.Position = UDim2.new(0.5, -175, 0.5, -260)
Main.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 20)

local CloseBtn = Instance.new("TextButton", Main)
CloseBtn.Size = UDim2.new(0, 30, 0, 30); CloseBtn.Position = UDim2.new(1, -35, 0, 10)
CloseBtn.Text = "X"; CloseBtn.BackgroundColor3 = Color3.fromRGB(180, 0, 0); CloseBtn.TextColor3 = Color3.new(1, 1, 1)
CloseBtn.Font = Enum.Font.GothamBold; Instance.new("UICorner", CloseBtn)

local OpenLogo = Instance.new("ImageButton", ScreenGui)
OpenLogo.Size = UDim2.new(0, 55, 0, 55); OpenLogo.Position = UDim2.new(0, 10, 0.5, -27)
OpenLogo.BackgroundColor3 = Color3.fromRGB(40, 30, 60); OpenLogo.Image = "rbxassetid://13543160877"; OpenLogo.Visible = false
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

CloseBtn.MouseButton1Click:Connect(function() Main.Visible = false; OpenLogo.Visible = true end)
OpenLogo.MouseButton1Click:Connect(function() Main.Visible = true; OpenLogo.Visible = false end)

local d, ds, sp; Main.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then d = true ds = i.Position sp = Main.Position end end)
UserInputService.InputChanged:Connect(function(i) if d and i.UserInputType == Enum.UserInputType.MouseMovement then local delta = i.Position - ds Main.Position = UDim2.new(sp.X.Scale, sp.X.Offset + delta.X, sp.Y.Scale, sp.Y.Offset + delta.Y) end end)
UserInputService.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then d = false end end)

local TabHolder = Instance.new("Frame", Main)
TabHolder.Size = UDim2.new(1, 0, 0, 45); TabHolder.BackgroundColor3 = Color3.fromRGB(25, 20, 40); Instance.new("UICorner", TabHolder)
local TabList = Instance.new("UIListLayout", TabHolder); TabList.FillDirection = Enum.FillDirection.Horizontal; TabList.HorizontalAlignment = Enum.HorizontalAlignment.Center; TabList.Padding = UDim.new(0, 10)

local Pages = Instance.new("Frame", Main); Pages.Size = UDim2.new(1, -20, 1, -80); Pages.Position = UDim2.new(0, 10, 0, 60); Pages.BackgroundTransparency = 1
local function CreatePage()
    local p = Instance.new("ScrollingFrame", Pages); p.Size = UDim2.new(1, 0, 1, 0); p.BackgroundTransparency = 1; p.Visible = false; p.ScrollBarThickness = 0
    Instance.new("UIListLayout", p).Padding = UDim.new(0, 8); return p
end
local PMain = CreatePage(); local PWorld = CreatePage(); local PTP = CreatePage(); PMain.Visible = true

local function AddTab(txt, pg)
    local b = Instance.new("TextButton", TabHolder); b.Size = UDim2.new(0, 80, 1, 0); b.Text = txt; b.TextColor3 = Color3.new(1,1,1); b.BackgroundTransparency = 1; b.Font = Enum.Font.GothamBold
    b.MouseButton1Click:Connect(function() PMain.Visible = false; PWorld.Visible = false; PTP.Visible = false; pg.Visible = true end)
end
AddTab("MAIN", PMain); AddTab("WORLD", PWorld); AddTab("TP", PTP)

local function AddBtn(parent, txt, cb)
    local b = Instance.new("TextButton", parent); b.Size = UDim2.new(1, 0, 0, 40); b.Text = txt; b.BackgroundColor3 = Color3.fromRGB(35, 30, 55); b.TextColor3 = Color3.new(1,1,1); b.Font = Enum.Font.GothamBold; Instance.new("UICorner", b)
    b.MouseButton1Click:Connect(function() cb(b) end); return b
end

-- [ أزرار التحكم ]
AddBtn(PMain, "🛡️ God Mode: OFF", function(b) godModeEnabled = not godModeEnabled; b.Text = godModeEnabled and "🛡️ God Mode: ON" or "🛡️ God Mode: OFF"; b.BackgroundColor3 = godModeEnabled and Color3.fromRGB(150, 0, 50) or Color3.fromRGB(35, 30, 55) end)
AddBtn(PMain, "🚫 No Ragdoll: OFF", function(b) noRagdollEnabled = not noRagdollEnabled; b.Text = noRagdollEnabled and "🚫 No Ragdoll: ON" or "🚫 No Ragdoll: OFF"; b.BackgroundColor3 = noRagdollEnabled and Color3.fromRGB(0, 100, 150) or Color3.fromRGB(35, 30, 55) end)
AddBtn(PMain, "🧱 NoClip: OFF", function(b) noclipEnabled = not noclipEnabled; b.Text = noclipEnabled and "🧱 NoClip: ON" or "🧱 NoClip: OFF"; b.BackgroundColor3 = noclipEnabled and Color3.fromRGB(0, 150, 200) or Color3.fromRGB(35, 30, 55) end)
AddBtn(PMain, "🦘 Infinity Jump: OFF", function(b) infJumpEnabled = not infJumpEnabled; b.Text = infJumpEnabled and "🦘 Infinity Jump: ON" or "🦘 Infinity Jump: OFF" end)

local SpdInput = Instance.new("TextBox", PMain); SpdInput.Size = UDim2.new(1, 0, 0, 35); SpdInput.PlaceholderText = "Speed Value..."; SpdInput.BackgroundColor3 = Color3.fromRGB(20, 20, 30); SpdInput.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", SpdInput)
AddBtn(PMain, "🚀 Stealth Speed: OFF", function(b) stealthSpeedEnabled = not stealthSpeedEnabled; speedValue = tonumber(SpdInput.Text) or 50; b.Text = stealthSpeedEnabled and "🚀 Stealth Speed: ON" or "🚀 Stealth Speed: OFF" end)

AddBtn(PWorld, "💤 Anti-AFK: ACTIVE ✅", function() end)
AddBtn(PWorld, "⚡ FPS BOOST", function(b) for _,v in pairs(game:GetDescendants()) do if v:IsA("BasePart") then v.Material = "SmoothPlastic" end end; b.Text = "✅ FPS BOOSTED" end)
AddBtn(PWorld, "👁️ Unlock Zoom", function() player.CameraMaxZoomDistance = 100000 end)

AddBtn(PTP, "📍 Save Position", function() if player.Character then savedPosition = player.Character.HumanoidRootPart.CFrame end end)
AddBtn(PTP, "🌀 Ghost Fast TP", function() if savedPosition then ghostTP(savedPosition) end end)
AddBtn(PTP, "🖱️ Click TP: OFF", function(b) clickTpEnabled = not clickTpEnabled; b.Text = clickTpEnabled and "🖱️ Click TP: ON" or "🖱️ Click TP: OFF" end)
AddBtn(PTP, "⚡ Instant E: OFF", function(b) instantInteractionEnabled = not instantInteractionEnabled; b.Text = instantInteractionEnabled and "⚡ Instant E: ON" or "⚡ Instant E: OFF"; if instantInteractionEnabled then for _,v in pairs(workspace:GetDescendants()) do if v:IsA("ProximityPrompt") then v.HoldDuration = 0 end end end end)

print("👑 RXT FINAL - NO RAGDOLL ADDED")
