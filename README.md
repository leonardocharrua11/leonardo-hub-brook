
local player = game.Players.LocalPlayer
local flying = false
local speed = 50

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 160)
frame.Position = UDim2.new(0, 30, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.BackgroundTransparency = 0.2
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "Leonardo Hub"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22
title.Parent = frame

local minimizado = false
local minimizar = Instance.new("TextButton")
minimizar.Text = "-"
minimizar.Size = UDim2.new(0, 30, 0, 30)
minimizar.Position = UDim2.new(1, -35, 0, 0)
minimizar.BackgroundColor3 = Color3.fromRGB(70,70,70)
minimizar.TextColor3 = Color3.fromRGB(255,255,255)
minimizar.Parent = frame

local flyBtn = Instance.new("TextButton")
flyBtn.Text = "Ativar/Desativar Voar"
flyBtn.Size = UDim2.new(0.9, 0, 0, 40)
flyBtn.Position = UDim2.new(0.05, 0, 0, 40)
flyBtn.BackgroundColor3 = Color3.fromRGB(80, 180, 80)
flyBtn.TextColor3 = Color3.fromRGB(255,255,255)
flyBtn.Font = Enum.Font.SourceSansBold
flyBtn.TextSize = 20
flyBtn.Parent = frame

local trollBtn = Instance.new("TextButton")
trollBtn.Text = "Trollar (Teleportar Todos)"
trollBtn.Size = UDim2.new(0.9, 0, 0, 40)
trollBtn.Position = UDim2.new(0.05, 0, 0, 90)
trollBtn.BackgroundColor3 = Color3.fromRGB(180, 80, 80)
trollBtn.TextColor3 = Color3.fromRGB(255,255,255)
trollBtn.Font = Enum.Font.SourceSansBold
trollBtn.TextSize = 18
trollBtn.Parent = frame

-- Minimizar/maximizar menu
minimizar.MouseButton1Click:Connect(function()
    minimizado = not minimizado
    flyBtn.Visible = not minimizado
    trollBtn.Visible = not minimizado
    if minimizado then
        frame.Size = UDim2.new(0, 220, 0, 30)
        minimizar.Text = "+"
    else
        frame.Size = UDim2.new(0, 220, 0, 160)
        minimizar.Text = "-"
    end
end)

-- Função de voo
function fly()
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local hrp = char.HumanoidRootPart

    flying = true
    while flying do
        local moveDirection = Vector3.new()
        if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
            moveDirection = moveDirection + workspace.CurrentCamera.CFrame.LookVector
        end
        if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
            moveDirection = moveDirection - workspace.CurrentCamera.CFrame.LookVector
        end
        if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.A) then
            moveDirection = moveDirection - workspace.CurrentCamera.CFrame.RightVector
        end
        if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.D) then
            moveDirection = moveDirection + workspace.CurrentCamera.CFrame.RightVector
        end
        if moveDirection.Magnitude > 0 then
            hrp.Velocity = moveDirection.Unit * speed
        else
            hrp.Velocity = Vector3.new(0,0,0)
        end
        wait(0.03)
    end
    hrp.Velocity = Vector3.new(0,0,0)
end

function stopFly()
    flying = false
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
    end
end

flyBtn.MouseButton1Click:Connect(function()
    if not flying then
        fly()
        flyBtn.BackgroundColor3 = Color3.fromRGB(60, 150, 225)
        flyBtn.Text = "Voando! (clique p/ parar)"
    else
        stopFly()
        flyBtn.BackgroundColor3 = Color3.fromRGB(80,180,80)
        flyBtn.Text = "Ativar/Desativar Voar"
    end
end)

-- Função Troll: teleportar outros jogadores
trollBtn.MouseButton1Click:Connect(function()
    local allPlayers = game.Players:GetPlayers()
    for _, plr in ipairs(allPlayers) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            plr.Character.HumanoidRootPart.CFrame = CFrame.new(math.random(-500,500), 100, math.random(-500,500))
        end
    end
end)
