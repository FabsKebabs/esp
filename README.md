local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local function DrawCircle()
    local circle = Drawing.new("Circle")
    circle.Visible = false
    circle.Color = Color3.fromRGB(0, 255, 0) -- Green bubble color
    circle.Thickness = 2
    circle.Transparency = 0.8
    circle.Filled = false
    return circle
end

local function CreateBubbleESP(player)
    if player == LocalPlayer then return end

    local circle = DrawCircle()

    local function Update()
        RunService.RenderStepped:Connect(function()
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = player.Character.HumanoidRootPart
                local position, onScreen = Camera:WorldToViewportPoint(hrp.Position)

                if onScreen then
                    circle.Visible = true
                    circle.Position = Vector2.new(position.X, position.Y)
                    circle.Radius = math.clamp(300 / position.Z, 30, 100) -- Adjust radius based on distance
                else
                    circle.Visible = false
                end
            else
                circle.Visible = false
            end
        end)
    end

    coroutine.wrap(Update)()
end

-- Initialize Bubble ESP for existing players
for _, player in pairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        CreateBubbleESP(player)
    end
end

-- Add Bubble ESP for new players
Players.PlayerAdded:Connect(function(player)
    CreateBubbleESP(player)
end)
