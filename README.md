local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local buyRemote = ReplicatedStorage:WaitForChild("RemoteEvents"):WaitForChild("BuyCharacter")

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

-- إنشاء الـ GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SimpleMenu"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

local function createButton(text, posY)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 220, 0, 50)
    btn.Position = UDim2.new(0.5, -110, 0.5, posY)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.BorderSizePixel = 2
    btn.BorderColor3 = Color3.fromRGB(0, 170, 255)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 20
    btn.Text = text
    btn.Parent = screenGui
    return btn
end

-- إنشاء الأزرار
local autoBuyBtn = createButton("شراء تلقائي (إيقاف)", -70)
local longJumpBtn = createButton("قفز طويل (إيقاف)", 0)
local speedBtn = createButton("سرعة (إيقاف)", 70)

-- حقوق 3zf أسفل الشاشة
local footerLabel = Instance.new("TextLabel")
footerLabel.Size = UDim2.new(0, 200, 0, 20)
footerLabel.Position = UDim2.new(1, -210, 1, -30)
footerLabel.BackgroundTransparency = 1
footerLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
footerLabel.Font = Enum.Font.SourceSansItalic
footerLabel.TextSize = 16
footerLabel.Text = "حقوق 3zf"
footerLabel.Parent = screenGui

local autoBuying = false
local longJumpOn = false
local speedOn = false

local function updateButtons()
    autoBuyBtn.Text = autoBuying and "شراء تلقائي (تشغيل)" or "شراء تلقائي (إيقاف)"
    longJumpBtn.Text = longJumpOn and "قفز طويل (تشغيل)" or "قفز طويل (إيقاف)"
    speedBtn.Text = speedOn and "سرعة (تشغيل)" or "سرعة (إيقاف)"
end

updateButtons()

autoBuyBtn.MouseButton1Click:Connect(function()
    autoBuying = not autoBuying
    updateButtons()
end)

longJumpBtn.MouseButton1Click:Connect(function()
    longJumpOn = not longJumpOn
    humanoid.JumpPower = longJumpOn and 200 or 50
    updateButtons()
end)

speedBtn.MouseButton1Click:Connect(function()
    speedOn = not speedOn
    humanoid.WalkSpeed = speedOn and 120 or 16
    updateButtons()
end)

-- دالة تحريك باتويين ناعمة
local function tweenToPosition(targetPos)
    local tweenInfo = TweenInfo.new(0.5, Enum.EasingStyle.Linear)
    local tween = TweenService:Create(hrp, tweenInfo, {CFrame = CFrame.new(targetPos)})
    tween:Play()
    return tween
end

local function isClose(pos1, pos2, dist)
    return (pos1 - pos2).Magnitude <= dist
end

RunService.Heartbeat:Connect(function()
    if not autoBuying then return end

    local charactersFolder = workspace:FindFirstChild("CharactersFolder")
    if not charactersFolder then return end

    local target = charactersFolder:FindFirstChild("Noobini Pizzanini")
    if not target then return end

    local targetHRP = target:FindFirstChild("HumanoidRootPart")
    if not targetHRP then return end

    local dist = (hrp.Position - targetHRP.Position).Magnitude
    if dist > 3 then
        tweenToPosition(targetHRP.Position)
    else
        humanoid:MoveTo(hrp.Position) -- وقف الحركة

        local start = tick()
        while tick() - start < 3 do
            if not isClose(hrp.Position, targetHRP.Position, 3) then
                return
            end
            task.wait(0.1)
        end

        buyRemote:FireServer("Noobini Pizzanini")
        task.wait(1)
    end
end)
