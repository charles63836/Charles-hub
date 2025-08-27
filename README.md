# Charles-hub
Blox fruit hacks
-- Charles Hub LocalScript
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer
local trainEvent = ReplicatedStorage:WaitForChild("CharlesHub_TrainEvent")

-- ScreenGui
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "CharlesHubGui"

-- Main frame
local frame = Instance.new("Frame", screenGui)
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.Position = UDim2.fromScale(0.5, 0.5)
frame.Size = UDim2.new(0, 380, 0, 220)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)

-- Title
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, -20, 0, 40)
title.Position = UDim2.new(0, 10, 0, 8)
title.BackgroundTransparency = 1
title.Text = "Charles Hub"
title.Font = Enum.Font.GothamBold
title.TextSize = 24
title.TextColor3 = Color3.fromRGB(230, 230, 230)

-- Continue button
local continueBtn = Instance.new("TextButton", frame)
continueBtn.Size = UDim2.new(0, 120, 0, 36)
continueBtn.Position = UDim2.new(1, -130, 1, -46)
continueBtn.Text = "Continue"
continueBtn.Font = Enum.Font.Gotham
continueBtn.TextSize = 18
continueBtn.BackgroundColor3 = Color3.fromRGB(60, 120, 255)

-- Main menu
local menu = Instance.new("Frame", screenGui)
menu.AnchorPoint = Vector2.new(0.5, 0.5)
menu.Position = UDim2.fromScale(0.5, 0.5)
menu.Size = UDim2.new(0, 520, 0, 360)
menu.Visible = false
menu.BackgroundColor3 = Color3.fromRGB(18, 18, 24)

-- Training panel
local trainingPanel = Instance.new("Frame", menu)
trainingPanel.Position = UDim2.new(0, 10, 0, 70)
trainingPanel.Size = UDim2.new(0, 300, 0, 220)
trainingPanel.BackgroundColor3 = Color3.fromRGB(28, 28, 36)

local startBtn = Instance.new("TextButton", trainingPanel)
startBtn.Size = UDim2.new(0, 120, 0, 36)
startBtn.Position = UDim2.new(0, 10, 0, 50)
startBtn.Text = "Start Training"

local stopBtn = Instance.new("TextButton", trainingPanel)
stopBtn.Size = UDim2.new(0, 120, 0, 36)
stopBtn.Position = UDim2.new(0, 150, 0, 50)
stopBtn.Text = "Stop Training"
stopBtn.TextColor3 = Color3.fromRGB(160, 160, 160)

local infoLabel = Instance.new("TextLabel", trainingPanel)
infoLabel.Size = UDim2.new(1, -20, 0, 30)
infoLabel.Position = UDim2.new(0, 10, 0, 100)
infoLabel.BackgroundTransparency = 1
infoLabel.Text = "Status: Idle"

-- Stats label
local statsLabel = Instance.new("TextLabel", menu)
statsLabel.Position = UDim2.new(0, 320, 0, 80)
statsLabel.Size = UDim2.new(0, 180, 0, 200)
statsLabel.BackgroundTransparency = 1
statsLabel.Text = "XP: --\nLevel: --"

-- Logic
local isTraining = false
local function updateStats()
	local stats = player:FindFirstChild("leaderstats")
	if stats and stats:FindFirstChild("XP") and stats:FindFirstChild("Level") then
		statsLabel.Text = string.format("XP: %d\nLevel: %d", stats.XP.Value, stats.Level.Value)
	else
		statsLabel.Text = "XP: --\nLevel: --"
	end
end

continueBtn.MouseButton1Click:Connect(function()
	screenGui.Enabled = false
	menu.Visible = true
	updateStats()
end)

startBtn.MouseButton1Click:Connect(function()
	if isTraining then return end
	isTraining = true
	startBtn.Text = "Training..."
	infoLabel.Text = "Status: Training"
	task.spawn(function()
		while isTraining do
			trainEvent:FireServer("RequestGrant")
			updateStats()
			task.wait(1)
		end
	end)
end)

stopBtn.MouseButton1Click:Connect(function()
	if not isTraining then return end
	isTraining = false
	startBtn.Text = "Start Training"
	infoLabel.Text = "Status: Idle"
	updateStats()
end)

-- Auto-update stats
task.spawn(function()
	while true do
		updateStats()
		task.wait(1.5)
	end
end)
