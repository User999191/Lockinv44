local esp_settings = {
    textsize = 15, -- Adjust the font size
    colour = Color3.new(255, 255, 255), -- Customize the text color (white in this case)
}

local gui = Instance.new("BillboardGui")
local esp = Instance.new("TextLabel", gui)

gui.Name = "CrackedESP"
gui.ResetOnSpawn = false
gui.AlwaysOnTop = true
gui.LightInfluence = 0
gui.Size = UDim2.new(1.75, 0, 1.75, 0)

esp.BackgroundColor3 = Color3.new(255, 255, 255)
esp.Text = "" -- The player's name will be displayed here
esp.Size = UDim2.new(0.0001, 0.00001, 0.0001, 0.00001)
esp.BorderSizePixel = 3
esp.BorderColor3 = esp_settings.colour
esp.Font = "GothamSemibold"
esp.TextSize = esp_settings.textsize
esp.TextColor3 = esp_settings.colour

game:GetService("RunService").RenderStepped:Connect(function()
    for _, player in pairs(game:GetService("Players"):GetPlayers()) do
        if player ~= game:GetService("Players").LocalPlayer and not player.Character.Head:FindFirstChild("CrackedESP") then
            esp.Text = player.Name
            gui:Clone().Parent = player.Character.Head
        end
    end
end)
