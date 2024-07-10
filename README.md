while wait(0) do 
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = game.Workspace.CurrentCamera

-- Configuration
local ESPEnabled = true -- Toggle ESP on/off
local DrawDistance = 5000000 -- Max distance to draw ESP

-- Drawing function
local function drawESP(player)
    local character = player.Character
    if not character or not character:IsDescendantOf(game.Workspace) then
        return
    end
    
    -- Check if ESP elements already exist
    local nameLabel = player:FindFirstChild("ESP_NameLabel")
    local healthBar = player:FindFirstChild("ESP_HealthBar")
    local distanceLabel = player:FindFirstChild("ESP_DistanceLabel")
    
    -- Calculate player's position on screen
    local head = character:FindFirstChild("Head")
    if not head then
        return
    end
    local headPos, onScreen = Camera:WorldToViewportPoint(head.Position)
    if not onScreen then
        return
    end
    
    -- Calculate distance
    local distance = (head.Position - Camera.CFrame.Position).Magnitude
    
    -- Draw or update ESP elements
    if not nameLabel then
        nameLabel = Drawing.new("Text")
        nameLabel.Name = "ESP_NameLabel"
        nameLabel.Visible = true
        nameLabel.Size = 18
        nameLabel.Color = Color3.fromRGB(255, 255, 255)
    end
    nameLabel.Text = player.Name
    nameLabel.Position = Vector2.new(headPos.X, headPos.Y - 30)
    
    if not healthBar then
        healthBar = Drawing.new("Square")
        healthBar.Name = "ESP_HealthBar"
        healthBar.Visible = true
        healthBar.Size = Vector2.new(100, 5)
        healthBar.Color = Color3.fromRGB(255, 0, 0)
    end
    local healthFraction = character.Humanoid.Health / character.Humanoid.MaxHealth
    healthBar.Size = Vector2.new(100 * healthFraction, 5)
    healthBar.Position = Vector2.new(headPos.X - 50, headPos.Y - 20)
    
    if not distanceLabel then
        distanceLabel = Drawing.new("Text")
        distanceLabel.Name = "ESP_DistanceLabel"
        distanceLabel.Visible = true
        distanceLabel.Size = 16
        distanceLabel.Color = Color3.fromRGB(255, 255, 255)
    end
    distanceLabel.Text = string.format("Distance: %.1f studs", distance)
    distanceLabel.Position = Vector2.new(headPos.X, headPos.Y - 50)
    
    -- Cleanup function (removes drawings when player is not visible or too far)
    local function cleanup()
        if nameLabel then
            nameLabel:Remove()
        end
        if healthBar then
            healthBar:Remove()
        end
        if distanceLabel then
            distanceLabel:Remove()
        end
    end
    
    -- Check visibility and distance continuously
    local connection
    connection = RunService.RenderStepped:Connect(function()
        if ESPEnabled and player.Character and player.Character:IsDescendantOf(game.Workspace) then
            local newHeadPos, newOnScreen = Camera:WorldToViewportPoint(head.Position)
            local newDistance = (head.Position - Camera.CFrame.Position).Magnitude
            
            if newOnScreen and newDistance <= DrawDistance then
                -- Update positions
                nameLabel.Position = Vector2.new(newHeadPos.X, newHeadPos.Y - 30)
                healthBar.Position = Vector2.new(newHeadPos.X - 50, newHeadPos.Y - 20)
                distanceLabel.Position = Vector2.new(newHeadPos.X, newHeadPos.Y - 50)
                
                -- Update health bar
                local newHealthFraction = character.Humanoid.Health / character.Humanoid.MaxHealth
                healthBar.Size = Vector2.new(100 * newHealthFraction, 5)
                
                -- Update distance label
                distanceLabel.Text = string.format("Distance: %.1f studs", newDistance)
            else
                -- Player is no longer visible or too far
                cleanup()
                connection:Disconnect()
            end
        else
            -- Player character was removed or is not in Workspace
            cleanup()
            connection:Disconnect()
        end
    end)
end

-- Function to handle new players joining
local function onPlayerAdded(player)
    if player ~= LocalPlayer then
        drawESP(player)
    end
end

-- Function to handle existing players in game
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        drawESP(player)
    end
end

Players.PlayerAdded:Connect(onPlayerAdded)
end)
