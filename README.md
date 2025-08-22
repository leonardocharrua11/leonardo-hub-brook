-- Leonardo Hub - Painel Estilo Drip Client, apenas Carro Arco-Íris
-- Este script é apenas para fins educacionais.

local player = game.Players.LocalPlayer
local rainbowActive = false
local rainbowThread = nil

-- Criar GUI principal estilo painel Drip Client
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 480, 0, 360)
mainFrame.Position = UDim2.new(0.5, -240, 0.5, -180)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0.13
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Barra superior com nome do hub e botão de fechar
local topBar = Instance.new("Frame")
topBar.Name = "TopBar"
topBar.Size = UDim2.new(1, 0, 0, 38)
topBar.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
topBar.BorderSizePixel = 0
topBar.Parent = mainFrame

local hubName = Instance.new("TextLabel")
hubName.Name = "HubName"
hubName.Size = UDim2.new(1, -40, 1, 0)
hubName.Position = UDim2.new(0, 16, 0, 0)
hubName.BackgroundTransparency = 1
hubName.Text = "Leonardo Hub"
hubName.TextXAlignment = Enum.TextXAlignment.Left
hubName.Font = Enum.Font.SourceSansBold
hubName.TextColor3 = Color3.fromRGB(255, 255, 255)
hubName.TextSize = 24
hubName.Parent = topBar

local closeBtn = Instance.new("TextButton")
closeBtn.Name = "CloseBtn"
closeBtn.Size = UDim2.new(0, 35, 0, 35)
closeBtn.Position = UDim2.new(1, -40, 0, 2)
closeBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.fromRGB(230, 230, 230)
closeBtn.TextSize = 24
closeBtn.Font = Enum.Font.SourceSans
closeBtn.Parent = topBar
closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Menu lateral (apenas visual, sem botões)
local sideMenu = Instance.new("Frame")
sideMenu.Name = "SideMenu"
sideMenu.Size = UDim2.new(0, 144, 1, -38)
sideMenu.Position = UDim2.new(0, 0, 0, 38)
sideMenu.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
sideMenu.BorderSizePixel = 0
sideMenu.Parent = mainFrame

local menuTitle = Instance.new("TextLabel")
menuTitle.Size = UDim2.new(1, 0, 0, 45)
menuTitle.BackgroundTransparency = 1
menuTitle.Text = "Menu"
menuTitle.Font = Enum.Font.SourceSansBold
menuTitle.TextColor3 = Color3.fromRGB(160, 160, 160)
menuTitle.TextSize = 22
menuTitle.Parent = sideMenu

-- Conteúdo central
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, -144, 1, -38)
contentFrame.Position = UDim2.new(0, 144, 0, 38)
contentFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
contentFrame.BackgroundTransparency = 0.10
contentFrame.BorderSizePixel = 0
contentFrame.Parent = mainFrame

-- Botão Carro Arco-Íris centralizado
local rainbowBtn = Instance.new("TextButton")
rainbowBtn.Name = "RainbowCarBtn"
rainbowBtn.Text = "Ativar Carro Arco-Íris"
rainbowBtn.Size = UDim2.new(0, 220, 0, 48)
rainbowBtn.Position = UDim2.new(0.5, -110, 0.3, 0)
rainbowBtn.BackgroundColor3 = Color3.fromRGB(200, 80, 200)
rainbowBtn.TextColor3 = Color3.fromRGB(255,255,255)
rainbowBtn.Font = Enum.Font.SourceSansBold
rainbowBtn.TextSize = 22
rainbowBtn.BorderSizePixel = 0
rainbowBtn.Parent = contentFrame

-- Descrição
local desc = Instance.new("TextLabel")
desc.Size = UDim2.new(1, -40, 0, 50)
desc.Position = UDim2.new(0, 20, 0.5, 18)
desc.BackgroundTransparency = 1
desc.Text = "Quando ativado, seu carro vai mudar de cor automaticamente!"
desc.TextColor3 = Color3.fromRGB(200,200,200)
desc.TextStrokeTransparency = 0.8
desc.Font = Enum.Font.SourceSans
desc.TextSize = 18
desc.TextWrapped = true
desc.Parent = contentFrame

-- Função para detectar o veículo do jogador (Brookhaven)
function getPlayerCar()
    if not player.Character then return nil end
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("VehicleSeat") and v.Occupant and v.Occupant.Parent == player.Character then
            return v.Parent
        end
    end
    return nil
end

-- Função para mudar cor de todas as peças possíveis do carro
function rainbowifyCar(car, hue)
    for _, part in ipairs(car:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            pcall(function()
                part.Color = Color3.fromHSV(hue, 1, 1)
            end)
        end
        if part:IsA("Decal") then
            pcall(function()
                part.Color3 = Color3.fromHSV(hue, 1, 1)
            end)
        end
        if part:IsA("ImageLabel") or part:IsA("ImageButton") then
            pcall(function()
                part.ImageColor3 = Color3.fromHSV(hue, 1, 1)
            end)
        end
    end
end

-- Loop do arco-íris
function startRainbowCar()
    if rainbowThread then return end
    rainbowActive = true
    rainbowBtn.Text = "Desativar Carro Arco-Íris"
    rainbowBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 120)
    rainbowThread = coroutine.create(function()
        while rainbowActive do
            local car = getPlayerCar()
            if car then
                local hue = (tick() * 0.2) % 1
                rainbowifyCar(car, hue)
            end
            wait(0.15)
        end
    end)
    coroutine.resume(rainbowThread)
end

function stopRainbowCar()
    rainbowActive = false
    rainbowBtn.Text = "Ativar Carro Arco-Íris"
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
