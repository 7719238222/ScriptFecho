local player = game.Players.LocalPlayer

-- Function to get the current instances (handles recreation)
local function getInstances()
    local yesButton, spawnButton, itemFrame
    
    local success, result = pcall(function()
        yesButton = player.PlayerGui.StartScreen.MainHolder.ConfirmPrompt.ConfirmPrompt.Yes
    end)
    if not success then yesButton = nil end
    
    local success2, result2 = pcall(function()
        spawnButton = player.PlayerGui.StartScreen.MainHolder.SelectCharacterFrame.LeftArea.BottomArea.Spawn
    end)
    if not success2 then spawnButton = nil end
    
    local success3, result3 = pcall(function()
        itemFrame = player.PlayerGui.StartScreen.MainHolder.SelectCharacterFrame.LeftArea.MiddleArea.BottomArea.ItemFrame
    end)
    if not success3 then itemFrame = nil end
    
    return yesButton, spawnButton, itemFrame
end

-- Function to click the buttons
local function clickYes()
    local yesButton, spawnButton = getInstances()
    
    if yesButton and yesButton:IsA('ImageButton') and spawnButton and spawnButton:IsA('ImageButton') then
        task.wait(0.5)
        firesignal(spawnButton.Activated)
        firesignal(yesButton.Activated)
        print('Fired signal')
    end
end

-- Function to set up connections for a specific itemFrame
local function setupItemFrame(itemFrame)
    if not itemFrame or not itemFrame.Parent then return end
    
    for _, button in ipairs(itemFrame:GetDescendants()) do
        if button:IsA('ImageButton') then
            button.MouseButton1Click:Connect(clickYes)
        end
    end
    
    itemFrame.DescendantAdded:Connect(function(descendant)
        if descendant:IsA('ImageButton') then
            descendant.MouseButton1Click:Connect(clickYes)
        end
    end)
end

-- Monitor for when itemFrame gets recreated
while true do
    local yesButton, spawnButton, itemFrame = getInstances()
    
    if itemFrame and itemFrame.Parent then
        -- Set up initial connections
        setupItemFrame(itemFrame)
        print("ItemFrame found and connections set up!")
        
        -- Wait for itemFrame to be removed/destroyed
        while itemFrame and itemFrame.Parent do
            task.wait(1)
        end
        print("ItemFrame was removed, waiting for new one...")
    end
    
    task.wait(0.5)
end
