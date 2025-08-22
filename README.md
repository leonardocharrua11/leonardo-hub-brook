-- Leonardo Hub - Apenas Carro Arco-íris
-- Este script é apenas para fins educacionais.

local player = game.Players.LocalPlayer
local rainbowActive = false
local rainbowThread = nil

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 240, 0, 100)
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

local rainbowBtn = Instance.new("TextButton")
rainbowBtn.Text = "Ativar Carro Arco-íris"
rainbowBtn.Size = UDim2.new(0.9, 0, 0, 40)
rainbowBtn.Position = UDim2.new(0.05, 0, 0, 40)
rainbowBtn.BackgroundColor3 = Color3.fromRGB(200, 80, 200)
rainbowBtn.TextColor3 = Color3.fromRGB(255,255,255)
rainbowBtn.Font = Enum.Font.SourceSansBold
rainbowBtn.TextSize = 18
rainbowBtn.Parent = frame

-- Minimizar/maximizar menu
minimizar.MouseButton1Click:Connect(function()
    minimizado = not minimizado
    rainbowBtn.Visible = not minimizado
    if minimizado then
        frame.Size = UDim2.new(0, 240, 0, 30)
        minimizar.Text = "+"
    else
        frame.Size = UDim2.new(0, 240, 0, 100)
        minimizar.Text = "-"
    end
end)

-- Função para detectar carro do jogador
function getPlayerCar()
    -- Procura por um veículo em que o jogador está sentado
    if not player.Character then return nil end
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("VehicleSeat") and v.Occupant and v.Occupant.Parent == player.Character then
            return v.Parent
        end
    end
    return nil
end

-- Função Arco-íris para o carro
function startRainbowCar()
    if rainbowThread then return end -- já está ativo
    rainbowActive = true
    rainbowBtn.Text = "Desativar Carro Arco-íris"
    rainbowBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 120)
    rainbowThread = coroutine.create(function()
        while rainbowActive do
            local car = getPlayerCar()
            if car then
                -- Muda a cor de todas as partes do carro
                for _, part in ipairs(car:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Color = Color3.fromHSV(tick()%5/5,1,1)
                    end
                end
            end
            wait(0.2)
        end
    end)
    coroutine.resume(rainbowThread)
end

function stopRainbowCar()
    rainbowActive = false
    rainbowBtn.Text = "Ativar Carro Arco-íris"
    rainbowBtn.BackgroundColor3 = Color3.fromRGB(200, 80, 200)
    rainbowThread = nil
end

rainbowBtn.MouseButton1Click:Connect(function()
    if rainbowActive then
        stopRainbowCar()
    else
        startRainbowCar()
    end
end)
