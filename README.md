local player = game.Players.LocalPlayer
local flying = false
local speed = 60

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 220)
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

local upBtn = Instance.new("TextButton")
upBtn.Text = "↑ Subir"
upBtn.Size = UDim2.new(0.42, 0, 0, 30)
upBtn.Position = UDim2.new(0.05, 0, 0, 90)
upBtn.BackgroundColor3 = Color3.fromRGB(60, 150, 225)
upBtn.TextColor3 = Color3.fromRGB(255,255,255)
upBtn.Font = Enum.Font.SourceSansBold
upBtn.TextSize = 18
upBtn.Parent = frame
upBtn.Visible = false

local downBtn = Instance.new("TextButton")
downBtn.Text = "↓ Descer"
downBtn.Size = UDim2.new(0.42, 0, 0, 30)
downBtn.Position = UDim2.new(0.53, 0, 0, 90)
downBtn.BackgroundColor3 = Color3.fromRGB(225, 150, 60)
downBtn.TextColor3 = Color3.fromRGB(255,255,255)
downBtn.Font = Enum.Font.SourceSansBold
downBtn.TextSize = 18
downBtn.Parent = frame
downBtn.Visible = false

local trollBtn = Instance.new("TextButton")
trollBtn.Text = "Trollar (Teleportar Todos)"
trollBtn.Size = UDim2.new(0.9, 0, 0, 40)
trollBtn.Position = UDim2.new(0.05, 0, 0, 130)
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
    upBtn.Visible = not minimizado and flying
    downBtn.Visible = not minimizado and flying
    if minimizado then
        frame.Size = UDim2.new(0, 220, 0, 30)
        minimizar.Text = "+"
    else
        frame.Size = UDim2.new(0, 220, 0, 220)
        minimizar.Text = "-"
    end
end)

-- Função de voo
local bodyVelocity
local verticalDirection = 0

function setBodyVelocity(vel)
    if not bodyVelocity and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(1,1,1) * 1e5
        bodyVelocity.Parent = player.Character.HumanoidRootPart
    end
    if bodyVelocity then
        bodyVelocity.Velocity = vel
    end
end

function removeBodyVelocity()
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
end

function fly()
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    flying = true
    upBtn.Visible = true
    downBtn.Visible = true
    while flying and char and char:FindFirstChild("HumanoidRootPart") do
        local cam = workspace.CurrentCamera
        local moveDir = Vector3.new()
        -- Mobile: direção da câmera
        if cam then
            moveDir = cam.CFrame.LookVector
        end
        -- Ajuste vertical
        moveDir = (moveDir * speed) + Vector3.new(0, verticalDirection * speed, 0)
        setBodyVelocity(moveDir)
        wait(0.03)
    end
    removeBodyVelocity()
    upBtn.Visible = false
    downBtn.Visible = false
end

function stopFly()
    flying = false
    removeBodyVelocity()
    upBtn.Visible = false
    downBtn.Visible = false
end

flyBtn.MouseButton1Click:Connect(function()
    if not flying then
        flyBtn.BackgroundColor3 = Color3.fromRGB(60, 150, 225)
        flyBtn.Text = "Voando! (clique p/ parar)"
        fly()
    else
        flyBtn.BackgroundColor3 = Color3.fromRGB(80,180,80)
        flyBtn.Text = "Ativar/Desativar Voar"
        stopFly()
    end
end)

upBtn.MouseButton1Down:Connect(function() verticalDirection = 1 end)
upBtn.MouseButton1Up:Connect(function() verticalDirection = 0 end)
downBtn.MouseButton1Down:Connect(function() verticalDirection = -1 end)
downBtn.MouseButton1Up:Connect(function() verticalDirection = 0 end)

-- Função Troll: teleportar outros jogadores
trollBtn.MouseButton1Click:Connect(function()
    local allPlayers = game.Players:GetPlayers()
    for _, plr in ipairs(allPlayers) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            plr.Character.HumanoidRootPart.CFrame = CFrame.new(math.random(-500,500), 100, math.random(-500,500))
        end
    end
end)
