local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local originalWalkSpeed = player.Character:WaitForChild("Humanoid").WalkSpeed
local originalJumpPower = player.Character:WaitForChild("Humanoid").JumpPower
local isNoclipEnabled = false
local isInfJumpEnabled = false
local noclipLoop = nil
local infJumpDebounce = false
local infJump = nil

local screenGui = Instance.new("ScreenGui")
screenGui.IgnoreGuiInset = true
screenGui.Parent = player.PlayerGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 200, 0, 320) -- Increased height for the noclip and inf jump buttons
mainFrame.Position = UDim2.new(1, -210, 0, 10) -- Top-right corner
mainFrame.BackgroundTransparency = 1.0
mainFrame.Parent = screenGui

local background = Instance.new("Frame")
background.BackgroundColor3 = Color3.fromRGB(30, 30, 30) -- Dark gray color for the background
background.BorderSizePixel = 0
background.Size = UDim2.new(1, 0, 1, 0)
background.Parent = mainFrame

local titleText = Instance.new("TextLabel")
titleText.Text = "Admin Panel"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255) -- White color for the title
titleText.Font = Enum.Font.SourceSansBold
titleText.TextSize = 18
titleText.Size = UDim2.new(1, 0, 0, 30)
titleText.BackgroundTransparency = 1.0
titleText.Parent = background

local speedButton = Instance.new("TextButton")
speedButton.Text = "Change Speed"
speedButton.Position = UDim2.new(0, 10, 0, 40)
speedButton.Size = UDim2.new(0, 180, 0, 30)
speedButton.Parent = background

local function changeSpeed()
    player.Character:WaitForChild("Humanoid").WalkSpeed = 100
end

speedButton.MouseButton1Click:Connect(changeSpeed)

local normalButton = Instance.new("TextButton")
normalButton.Text = "Back to Normal"
normalButton.Position = UDim2.new(0, 10, 0, 80)
normalButton.Size = UDim2.new(0, 180, 0, 30)
normalButton.Parent = background

local function restoreNormal()
    player.Character:WaitForChild("Humanoid").WalkSpeed = originalWalkSpeed
    player.Character:WaitForChild("Humanoid").JumpPower = originalJumpPower
end

normalButton.MouseButton1Click:Connect(restoreNormal)

local infJumpButton = Instance.new("TextButton")
infJumpButton.Text = "Toggle Infinite Jump"
infJumpButton.Position = UDim2.new(0, 10, 0, 120)
infJumpButton.Size = UDim2.new(0, 180, 0, 30)
infJumpButton.Parent = background

local function toggleInfJump()
    isInfJumpEnabled = not isInfJumpEnabled
end

infJumpButton.MouseButton1Click:Connect(toggleInfJump)

local jumpButton = Instance.new("TextButton")
jumpButton.Text = "Change Jump Power"
jumpButton.Position = UDim2.new(0, 10, 0, 160)
jumpButton.Size = UDim2.new(0, 180, 0, 30)
jumpButton.Parent = background

local function changeJumpPower()
    player.Character:WaitForChild("Humanoid").JumpPower = 150
end

jumpButton.MouseButton1Click:Connect(changeJumpPower)

local noclipButton = Instance.new("TextButton")
noclipButton.Text = "Toggle Noclip"
noclipButton.Position = UDim2.new(0, 10, 0, 200)
noclipButton.Size = UDim2.new(0, 180, 0, 30)
noclipButton.Parent = background

local function toggleNoclipLoop()
    local character = player.Character
    local floatName = "Floating"
    while isNoclipEnabled do
        if character and character:FindFirstChild("HumanoidRootPart") then
            local rootPart = character:WaitForChild("HumanoidRootPart")
            local speaker = rootPart:FindFirstChild("Humanoid")
            for _, child in pairs(character:GetDescendants()) do
                if child:IsA("BasePart") and child.Name ~= floatName then
                    if isNoclipEnabled then
                        child.CanCollide = false
                    else
                        child.CanCollide = true
                    end
                end
            end
        end
        wait()
    end
end

noclipButton.MouseButton1Click:Connect(function()
    isNoclipEnabled = not isNoclipEnabled
    if isNoclipEnabled then
        toggleNoclipLoop()
    else
        if noclipLoop then
            noclipLoop:Disconnect()
            noclipLoop = nil
        end
    end
end)

local disableNoclipButton = Instance.new("TextButton")
disableNoclipButton.Text = "Disable Noclip"
disableNoclipButton.Position = UDim2.new(0, 10, 0, 240)
disableNoclipButton.Size = UDim2.new(0, 180, 0, 30)
disableNoclipButton.Parent = background

disableNoclipButton.MouseButton1Click:Connect(function()
    if noclipLoop then
        noclipLoop:Disconnect()
        noclipLoop = nil
    end
    isNoclipEnabled = false
end)

local uninfJumpButton = Instance.new("TextButton")
uninfJumpButton.Text = "Uninfinitize Jump"
uninfJumpButton.Position = UDim2.new(0, 10, 0, 280)
uninfJumpButton.Size = UDim2.new(0, 180, 0, 30)
uninfJumpButton.Parent = background

local function uninfJump()
    if infJump then
        infJump:Disconnect()
        infJump = nil
        isInfJumpEnabled = false
    end
end

uninfJumpButton.MouseButton1Click:Connect(uninfJump)

local destroyGuiButton = Instance.new("TextButton")
destroyGuiButton.Text = "Destroy GUI"
destroyGuiButton.Position = UDim2.new(0, 10, 0, 320)
destroyGuiButton.Size = UDim2.new(0, 180, 0, 30)
destroyGuiButton.Parent = background
destroyGuiButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Set the button color to red

destroyGuiButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

local function onJumpRequest()
    if isInfJumpEnabled then
        local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
        if humanoid and not infJumpDebounce then
            infJumpDebounce = true
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            wait()
            infJumpDebounce = false
        end
    end
end

infJump = UserInputService.JumpRequest:Connect(onJumpRequest)

local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local function enableDragging(gui)
    local dragging
    local dragInput
    local dragStart
    local startPos
    local function update(input)
        local delta = input.Position - dragStart
        gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    gui.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end

enableDragging(mainFrame)
