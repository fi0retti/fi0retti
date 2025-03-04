local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local function getTeamColor(player)
    if player.TeamColor == BrickColor.new("Bright red").Color then
        return "Red"
    elseif player.TeamColor == BrickColor.new("Bright blue").Color then
        return "Blue"
    end
    return "Unknown"
end

local function drawESP(player)
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local screenPos, onScreen = Camera:WorldToViewportPoint(character.HumanoidRootPart.Position)
        if onScreen then
            local marker = Drawing.new("Square")
            marker.Size = Vector2.new(100, 100)
            marker.Position = Vector2.new(screenPos.X - 50, screenPos.Y - 50)
            marker.Color = Color3.new(1, 0, 0)
            marker.Thickness = 2
            marker.Visible = true
            marker.Parent = game:GetService("CoreGui")
        end
    end
end

local function updateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and getTeamColor(player) ~= getTeamColor(LocalPlayer) then
            drawESP(player)
        end
    end
end

local function toggleScript()
    if scriptEnabled then
        for _, drawing in pairs(game:GetService("CoreGui"):GetChildren()) do
            if drawing:IsA("Drawing") then
                drawing:Destroy()
            end
        end
    else
        RunService.RenderStepped:Connect(updateESP)
    end
    scriptEnabled = not scriptEnabled
end

local scriptEnabled = false

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.P then
        toggleScript()
    end
end)
