-- TF2 Movement Utility Script with Scout Themed UI (Rayfield Version, Blue Theme)
-- Made for Roblox using Rayfield UI Library

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Debris = game:GetService("Debris")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local HRP = Character:WaitForChild("HumanoidRootPart")

-- Load Rayfield UI
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
    Name = "Scout Utility",
    LoadingTitle = "TF2 Scout Script",
    LoadingSubtitle = "by RobloxGuyOrSmth",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "ScoutFlyConfig"
    },
    Discord = {
        Enabled = false
    },
    KeySystem = false
})

-- Scout Sounds
local function playScoutSound(type)
    local soundId = type == "yes" and "9118823102" or "9118821973"
    local sound = Instance.new("Sound", workspace)
    sound.SoundId = "rbxassetid://" .. soundId
    sound:Play()
    Debris:AddItem(sound, 2)
end

-- Random Roblox sounds for fun button
local soundLibrary = {
    12222030, 130797915, 138186576, 9118823102
}

local function playRandomSound()
    local id = soundLibrary[math.random(1, #soundLibrary)]
    local s = Instance.new("Sound", workspace)
    s.SoundId = "rbxassetid://" .. id
    s:Play()
    Debris:AddItem(s, 5)
end

-- Movement tab
local MovementTab = Window:CreateTab("Movement", 4483362458)

-- Walk speed setup
local walkSpeed = 16
MovementTab:CreateSlider({
    Name = "Walk Speed",
    Range = {1, 100},
    Increment = 1,
    CurrentValue = walkSpeed,
    Callback = function(v)
        walkSpeed = v
        Humanoid.WalkSpeed = v
    end
})

-- Fly setup
local flySpeed = 50
local flying = false
local vel = Instance.new("BodyVelocity")
vel.MaxForce = Vector3.new(1,1,1)*math.huge
vel.P = 12500

local directions = {w=false,a=false,s=false,d=false,space=false,shift=false}

UIS.InputBegan:Connect(function(input, gpe)
    if not gpe and directions[input.KeyCode.Name:lower()] ~= nil then
        directions[input.KeyCode.Name:lower()] = true
    end
end)
UIS.InputEnded:Connect(function(input, gpe)
    if directions[input.KeyCode.Name:lower()] ~= nil then
        directions[input.KeyCode.Name:lower()] = false
    end
end)

MovementTab:CreateToggle({
    Name = "Fly",
    CurrentValue = false,
    Callback = function(val)
        flying = val
        if val then
            playScoutSound("yes")
            vel.Parent = HRP
            RunService.RenderStepped:Connect(function()
                if flying then
                    local cam = workspace.CurrentCamera.CFrame
                    local move = Vector3.zero
                    if directions.w then move += cam.LookVector end
                    if directions.s then move -= cam.LookVector end
                    if directions.a then move -= cam.RightVector end
                    if directions.d then move += cam.RightVector end
                    if directions.space then move += Vector3.new(0,1,0) end
                    if directions.shift then move -= Vector3.new(0,1,0) end
                    vel.Velocity = move.Unit * flySpeed
                end
            end)
        else
            playScoutSound("no")
            vel.Velocity = Vector3.zero
            vel.Parent = nil
        end
    end
})

MovementTab:CreateSlider({
    Name = "Fly Speed",
    Range = {1, 100},
    Increment = 1,
    CurrentValue = flySpeed,
    Callback = function(v)
        flySpeed = v
    end
})

-- Noclip
local noclip = false
local NoclipConnection
MovementTab:CreateToggle({
    Name = "Noclip",
    CurrentValue = false,
    Callback = function(val)
        noclip = val
        if val then
            playScoutSound("yes")
            NoclipConnection = RunService.Stepped:Connect(function()
                if Humanoid then
                    Humanoid:ChangeState(11)
                end
            end)
        else
            playScoutSound("no")
            if NoclipConnection then NoclipConnection:Disconnect() end
        end
    end
})

-- Invisibility toggle
local invisible = false
MovementTab:CreateToggle({
    Name = "Invisibility",
    CurrentValue = false,
    Callback = function(val)
        invisible = val
        playScoutSound(val and "yes" or "no")
        for _, part in ipairs(Character:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = val and 1 or 0
            end
        end
    end
})

-- Fun tab
local FunTab = Window:CreateTab("Fun", 4483362458)

-- Button that does nothing
local clickCount = 0
FunTab:CreateButton({
    Name = "Button That Does Nothing",
    Callback = function()
        clickCount += 1
        if clickCount == 5 then
            FunTab:CreateButton({
                Name = "Really, That Does Nothing",
                Callback = function() end
            })
        end
    end
})

-- Random sound button
FunTab:CreateButton({
    Name = "Play Random Roblox Sound",
    Callback = function()
        playRandomSound()
    end
})
