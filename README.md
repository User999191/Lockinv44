-- Function to calculate the corners of the 3D ESP box with a fixed size
local function calculate3DBoxCorners(position, size)
    local halfSize = Vector3.new(2.5, 2.5, 2.5)  -- Half of the fixed size (5x5x5)
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

-- Function to update 3D ESP for a single player
local function update3DESP(player)
    -- Check if the player has a character and it is not the local player
    if player.Character and player ~= game.Players.LocalPlayer then
        local character = player.Character
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")

        -- Check if the player's character has a humanoid root part
        if humanoidRootPart then
            -- Calculate screen position for the ESP
            local position = humanoidRootPart.Position
            local sizeVector = Vector3.new(5, 5, 5)  -- Fixed size (5x5x5)
            local boxCorners = calculate3DBoxCorners(position, sizeVector)

            -- Check if any corner of the box is in view
            local inView = false
            for _, corner in pairs(boxCorners) do
                local screenPosition, onScreen = workspace.CurrentCamera:WorldToViewportPoint(corner)
                if onScreen then
                    inView = true
                    break
                end
            end

            -- Create or update 3D ESP box
            local espBox = character:FindFirstChild("3DESPBox")
            if inView then
                if not espBox then
                    espBox = Instance.new("BoxHandleAdornment")
                    espBox.Name = "3DESPBox"
                    espBox.Adornee = character
                    espBox.Size = sizeVector
                    espBox.AlwaysOnTop = true
                    espBox.ZIndex = 5
                    espBox.Color3 = Color3.new(1, 1, 0) -- Yellow color
                    espBox.Transparency = 0.5
                    espBox.Parent = character
                else
                    espBox.Size = sizeVector
                    espBox.Color3 = Color3.new(1, 1, 0) -- Yellow color
                    espBox.Transparency = 0.5
                end

                -- Calculate and display distance
                local localPosition = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
                local dist = distance(localPosition, position)

                -- Create or update name label with distance
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
                    nameLabel.Text = player.Name .. "\nDistance: " .. string.format("%.1f", dist) .. " studs"
                    nameLabel.TextColor3 = Color3.new(1, 1, 1)
                    nameLabel.TextStrokeTransparency = 0.5
                    nameLabel.TextScaled = true
                    nameLabel.Parent = nameTag
                else
                    local nameLabel = nameTag:FindFirstChild("NameLabel")
                    if nameLabel then
                        nameLabel.Text = player.Name .. "\nDistance: " .. string.format("%.1f", dist) .. " studs"
                    end
                end
            else
                -- Player is off-screen or not visible, hide or remove ESP box and name label
                if espBox then
                    espBox:Destroy()
                end

                local nameTag = character:FindFirstChild("NameTag")
                if nameTag then
                    nameTag:Destroy()
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
