-- Function to calculate the corners of the 3D ESP box with a fixed size
local function calculate3DBoxCorners(position, size)
    local halfSize = size / 2
    return {
        Vector3.new(position.X - halfSize.X, position.Y - halfSize.Y, position.Z - halfSize.Z),
        Vector3.new(position.X + halfSize.X, position.Y - halfSize.Y, position.Z - halfSize.Z),
        Vector3.new(position.X + halfSize.X, position.Y + halfSize.Y, position.Z - halfSize.Z),
        Vector3.new(position.X - halfSize.X, position.Y + halfSize.Y, position.Z - halfSize.Z),
        Vector3.new(position.X - halfSize.X, position.Y - halfSize.Y, position.Z + halfSize.Z),
        Vector3.new(position.X + halfSize.X, position.Y - halfSize.Y, position.Z + halfSize.Z),
        Vector3.new(position.X + halfSize.X, position.Y + halfSize.Y, position.Z + halfSize.Z),
        Vector3.new(position.X - halfSize.X, position.Y + halfSize.Y, position.Z + halfSize.Z)
    }
end

-- Function to calculate distance between two points in 3D space
local function distance(point1, point2)
    return (point1 - point2).magnitude
end

local function update3DESP(player)
    if player.Character and player.Character.Parent then
        local character = player.Character
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart then
            local position = humanoidRootPart.Position
            local sizeVector = Vector3.new(5, 5, 5) -- Fixed size (5x5x5)
            local boxCorners = calculate3DBoxCorners(position, sizeVector)

            local inView = false
            for _, corner in pairs(boxCorners) do
                local screenPosition, onScreen = workspace.CurrentCamera:WorldToViewportPoint(corner)
                if onScreen then
                    inView = true
                    break
                end
            end

            local espBox = character:FindFirstChild("3DESPBox")
            if inView then
                if not espBox then
                    espBox = Instance.new("BoxHandleAdornment")
                    espBox.Name = "3DESPBox"
                    espBox.Adornee = character
                    espBox.Size = sizeVector
                    espBox.AlwaysOnTop = true
                    espBox.ZIndex = 5
                    espBox.Transparency = 0.5
                    espBox.Parent = character
                end

                local localPosition = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character.HumanoidRootPart.Position
                if localPosition then
                    local dist = distance(localPosition, position)
                    updateHealthNum(character, character.Humanoid.Health)

                    local nameTag = character:FindFirstChild("NameTag")
                    if not nameTag then
                        nameTag = Instance.new("BillboardGui")
                        nameTag.Name = "NameTag"
                        nameTag.Size = UDim2.new(0, 100, 0, 20)
                        nameTag.StudsOffset = Vector3.new(0, 3, 0) -- Offset above the character's head
                        nameTag.Adornee = character.Head
                        nameTag.AlwaysOnTop = true
                        nameTag.Parent = character

                        local nameLabel = Instance.new("TextLabel")
                        nameLabel.Name = "NameLabel"
                        nameLabel.Size = UDim2.new(1, 0, 1, 0)
                        nameLabel.BackgroundTransparency = 1
                        nameLabel.TextColor3 = Color3.new(1, 1, 1)
                        nameLabel.TextStrokeTransparency = 0.5
                        nameLabel.TextScaled = true
                        nameLabel.Parent = nameTag
                    end
                    -- Update name label with distance
                    nameTag.NameLabel.Text = player.Name .. "\nDistance: " .. string.format("%.1f", dist) .. " studs"

                    local healthBar = character:FindFirstChild("HealthBar")
                    if healthBar then
                        -- Update HealthBar if exists
                    end

                    local highlight = character:FindFirstChild("Highlight")
                    if not highlight then
                        -- Create Highlight if not exists
                    end
                    local greatHighlight = character:FindFirstChild("GreatHighlight")
                    if not greatHighlight then
                        -- Create GreatHighlight if not exists
                    end
                end
            else
                -- Hide or remove ESP components if not in view
            end
        end
    end
end

                    -- Create or update health bar
                    local healthBar = character:FindFirstChild("HealthBar")
                    if not healthBar then
                        healthBar = Instance.new("BillboardGui")
                        healthBar.Name = "HealthBar"
                        healthBar.Size = UDim2.new(0, 100, 0, 10)
                        healthBar.StudsOffset = Vector3.new(0, 2.5, 0) -- Offset above the character's head
                        healthBar.Adornee = character.Head
                        healthBar.AlwaysOnTop = true
                        healthBar.Parent = character

                        local healthFrame = Instance.new("Frame")
                        healthFrame.Name = "HealthFrame"
                        healthFrame.Size = UDim2.new(1, 0, 1, 0)
                        healthFrame.BackgroundColor3 = Color3.new(1, 0, 0) -- Red color
                        healthFrame.BorderSizePixel = 0
                        healthFrame.Parent = healthBar

                        local function updateHealthNum(character, health)
    local healthNum = character:FindFirstChild("HealthNum")
    if not healthNum then
        healthNum = Instance.new("BillboardGui")
        healthNum.Name = "HealthNum"
        healthNum.Size = UDim2.new(0, 50, 0, 20)
        healthNum.StudsOffset = Vector3.new(0, 2, 0) -- Offset above the character's head
        healthNum.Adornee = character.Head
        healthNum.AlwaysOnTop = true
        healthNum.Parent = character

        local healthLabel = Instance.new("TextLabel")
        healthLabel.Name = "HealthLabel"
        healthLabel.Size = UDim2.new(1, 0, 1, 0)
        healthLabel.BackgroundTransparency = 1
        healthLabel.TextColor3 = Color3.new(1, 1, 1)
        healthLabel.TextStrokeTransparency = 0.5
        healthLabel.TextScaled = true
        healthLabel.Parent = healthNum
    end
    -- Update health value
    healthNum.HealthLabel.Text = "Health: " .. math.floor(health)
end                         
                        local healthFill = Instance.new("Frame")
                        healthFill.Name = "HealthFill"
                        healthFill.Size = UDim2.new(1, 0, 1, 0)
                        healthFill.BackgroundColor3 = Color3.new(0, 1, 0) -- Green color
                        healthFill.BorderSizePixel = 0
                        healthFill.Parent = healthFrame
                    else
                        local healthFill = healthBar.HealthFrame.HealthFill
                        if healthFill then
                            local humanoid = character:FindFirstChildOfClass("Humanoid")
                            if humanoid then
                                healthFill.Size = UDim2.new(humanoid.Health / humanoid.MaxHealth, 0, 1, 0)
                            end
                        end
                    end

                    -- Highlight and Great Highlight system
                    local highlight = character:FindFirstChild("Highlight")
                    if not highlight then
                        highlight = Instance.new("BoxHandleAdornment")
                        highlight.Name = "Highlight"
                        highlight.Adornee = character
                        highlight.Size = sizeVector * 1.1  -- Slightly larger than the ESP box
                        highlight.AlwaysOnTop = true
                        highlight.ZIndex = 5
                        highlight.Transparency = 0.7
                        highlight.Color3 = Color3.new(1, 1, 0)  -- Yellow color
                        highlight.Parent = character
                    end

                    local greatHighlight = character:FindFirstChild("GreatHighlight")
                    if not greatHighlight then
                        greatHighlight = Instance.new("BoxHandleAdornment")
                        greatHighlight.Name = "GreatHighlight"
                        greatHighlight.Adornee = character
                        greatHighlight.Size = sizeVector * 1.2  -- Larger than the ESP box
                        greatHighlight.AlwaysOnTop = true
                        greatHighlight.ZIndex = 5
                        greatHighlight.Transparency = 0.5
                        greatHighlight.Color3 = Color3.new(1, 1, 0)  -- Yellow color
                        greatHighlight.Parent = character
                    end

                else
                    -- Local player's position not available, handle accordingly
                end
            else
                -- Player is off-screen or not visible, hide or remove ESP box, name label, health bar, and system health ESP
                if espBox then
                    espBox:Destroy()
                end

                local nameTag = character:FindFirstChild("NameTag")
                if nameTag then
                    nameTag:Destroy()
                end

                local healthBar = character:FindFirstChild("HealthBar")
                if healthBar then
                    healthBar:Destroy()
                end

                local highlight = character:FindFirstChild("Highlight")
                if highlight then
                    highlight:Destroy()
                end

                local greatHighlight = character:FindFirstChild("GreatHighlight")
                if greatHighlight then
                    greatHighlight:Destroy()
                end
            end
        end
    end
end

-- Function to handle 3D ESP for all players
local function updateAllPlayers3DESP()
    for _, player in ipairs(game.Players:GetPlayers()) do
        update3DESP(player)
    end
end

-- Example: Continuously update 3D ESP for all players
while true do
    updateAllPlayers3DESP()
    wait(1)  -- Adjust the interval based on how frequently you want to update
end
