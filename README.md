-- Leonardo Hub - Estilo Drip Client, Carro Arco-Íris, Minimizar, e Aviso de Dono

local player = game.Players.LocalPlayer
local rainbowActive = false
local rainbowThread = nil
local minimized = false

-- Criar GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

-- Painel principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 600, 0, 300)
mainFrame.Position = UDim2.new(0.5, -300, 0.5, -150)
mainFrame.BackgroundColor3 = Color3.fromRGB(23, 23, 23)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Top bar
local topBar = Instance.new("Frame")
topBar.Name = "TopBar"
topBar.Size = UDim2.new(1, 0, 0, 46)
topBar.Position = UDim2.new(0, 0, 0, 0)
topBar.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
topBar.BorderSizePixel = 0
topBar.Parent = mainFrame

local hubName = Instance.new("TextLabel")
hubName.Name = "HubName"
hubName.Size = UDim2.new(1, 0, 1, 0)
hubName.Position = UDim2.new(0, 18, 0, 0)
hubName.BackgroundTransparency = 1
hubName.Text = "Leonardo Hub"
hubName.TextXAlignment = Enum.TextXAlignment.Left
hubName.Font = Enum.Font.GothamBold
hubName.TextColor3 = Color3.fromRGB(255,255,255)
hubName.TextSize = 26
hubName.Parent = topBar

-- Dono aviso
local donoLabel = Instance.new("TextLabel")
donoLabel.Name = "DonoLabel"
donoLabel.Size = UDim2.new(1, -20, 0, 22)
donoLabel.Position = UDim2.new(0, 10, 0, 46)
donoLabel.BackgroundTransparency = 1
donoLabel.Text = "Dono do Leonardo Hub: peixebometop"
donoLabel.TextXAlignment = Enum.TextXAlignment.Left
donoLabel.Font = Enum.Font.GothamSemibold
donoLabel.TextColor3 = Color3.fromRGB(180,180,255)
donoLabel.TextSize = 18
donoLabel.Parent = mainFrame

-- Botão minimizar/maximizar
local minBtn = Instance.new("TextButton")
minBtn.Name = "MinBtn"
minBtn.Size = UDim2.new(0, 35, 0, 35)
minBtn.Position = UDim2.new(1, -42, 0, 6)
minBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
minBtn.Text = "_"
minBtn.TextColor3 = Color3.fromRGB(230, 230, 230)
minBtn.TextSize = 24
minBtn.Font = Enum.Font.SourceSans
minBtn.Parent = topBar

local function setMinimized(state)
    minimized = state
    if minimized then
        mainFrame.Size = UDim2.new(0, 260, 0, 46)
        donoLabel.Visible = false
        for _, v in ipairs(mainFrame:GetChildren()) do
            if v ~= topBar and v ~= donoLabel then
                v.Visible = false
            end
        end
        minBtn.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 600, 0, 300)
        donoLabel.Visible = true
        for _, v in ipairs(mainFrame:GetChildren()) do
            if v ~= topBar and v ~= donoLabel then
                v.Visible = true
            end
        end
        minBtn.Text = "_"
    end
end
minBtn.MouseButton1Click:Connect(function()
    setMinimized(not minimized)
end)
setMinimized(false)

-- Conteúdo central (apenas o botão arco-íris)
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, 0, 1, -68)
contentFrame.Position = UDim2.new(0, 0, 0, 68)
contentFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
contentFrame.BackgroundTransparency = 0.13
contentFrame.BorderSizePixel = 0
contentFrame.Parent = mainFrame

local rainbowBtn = Instance.new("TextButton")
rainbowBtn.Name = "RainbowCarBtn"
rainbowBtn.Text = "Ativar Carro Arco-Íris"
rainbowBtn.Size = UDim2.new(0, 260, 0, 55)
rainbowBtn.Position = UDim2.new(0.5, -130, 0.2, 0)
rainbowBtn.BackgroundColor3 = Color3.fromRGB(200, 80, 200)
rainbowBtn.TextColor3 = Color3.fromRGB(255,255,255)
rainbowBtn.Font = Enum.Font.GothamBold
rainbowBtn.TextSize = 22
rainbowBtn.BorderSizePixel = 0
rainbowBtn.Parent = contentFrame

-- Descrição
local desc = Instance.new("TextLabel")
desc.Size = UDim2.new(1, -40, 0, 50)
desc.Position = UDim2.new(0, 20, 0.4, 18)
desc.BackgroundTransparency = 1
desc.Text = "Ative e, ao entrar num carro, ele vai tentar mudar de cor automaticamente!"
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

-- Tentativa máxima para mudar cor de todas as peças do carro
function rainbowifyCar(car, hue)
    for _, part in ipairs(car:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("MeshPart") then
            pcall(function()
                part.Color = Color3.fromHSV(hue, 1, 1)
            end)
            if part:FindFirstChildOfClass("SpecialMesh") then
                pcall(function()
                    part:FindFirstChildOfClass("SpecialMesh").VertexColor = Vector3.new(hue,1,1)
                end)
            end
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
            wait(0.12)
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
