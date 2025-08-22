-- Leonardo Hub - Painel estilo Drip Client com aba Músicas (tocar som por ID)

local player = game.Players.LocalPlayer
local rainbowActive = false
local rainbowThread = nil
local soundObj = nil

-- Criar GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LeonardoHub"
screenGui.Parent = game.CoreGui

-- Painel principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 600, 0, 400)
mainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
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

local closeBtn = Instance.new("TextButton")
closeBtn.Name = "CloseBtn"
closeBtn.Size = UDim2.new(0, 35, 0, 35)
closeBtn.Position = UDim2.new(1, -42, 0, 6)
closeBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.fromRGB(230, 230, 230)
closeBtn.TextSize = 24
closeBtn.Font = Enum.Font.SourceSans
closeBtn.Parent = topBar
closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
    if soundObj then soundObj:Destroy() end
end)

-- Menu lateral com duas categorias
local sideMenu = Instance.new("Frame")
sideMenu.Name = "SideMenu"
sideMenu.Size = UDim2.new(0, 144, 1, -46)
sideMenu.Position = UDim2.new(0, 0, 0, 46)
sideMenu.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
sideMenu.BorderSizePixel = 0
sideMenu.Parent = mainFrame

-- Botão "Menu" (Rainbow Car)
local menuBtn = Instance.new("TextButton")
menuBtn.Size = UDim2.new(1, 0, 0, 45)
menuBtn.Position = UDim2.new(0, 0, 0, 0)
menuBtn.BackgroundTransparency = 1
menuBtn.Text = "Menu"
menuBtn.Font = Enum.Font.GothamBold
menuBtn.TextColor3 = Color3.fromRGB(160, 160, 160)
menuBtn.TextSize = 22
menuBtn.Name = "MenuBtn"
menuBtn.Parent = sideMenu

-- Botão "Músicas"
local musicBtn = Instance.new("TextButton")
musicBtn.Size = UDim2.new(1, 0, 0, 45)
musicBtn.Position = UDim2.new(0, 0, 0, 45)
musicBtn.BackgroundTransparency = 1
musicBtn.Text = "Músicas"
musicBtn.Font = Enum.Font.GothamBold
musicBtn.TextColor3 = Color3.fromRGB(160, 160, 160)
musicBtn.TextSize = 22
musicBtn.Name = "MusicBtn"
musicBtn.Parent = sideMenu

-- Conteúdo central para Rainbow Car
local contentRainbow = Instance.new("Frame")
contentRainbow.Name = "ContentRainbow"
contentRainbow.Size = UDim2.new(1, -144, 1, -46)
contentRainbow.Position = UDim2.new(0, 144, 0, 46)
contentRainbow.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
contentRainbow.BackgroundTransparency = 0.10
contentRainbow.BorderSizePixel = 0
contentRainbow.Parent = mainFrame

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
rainbowBtn.Parent = contentRainbow

local descRainbow = Instance.new("TextLabel")
descRainbow.Size = UDim2.new(1, -40, 0, 50)
descRainbow.Position = UDim2.new(0, 20, 0.5, 18)
descRainbow.BackgroundTransparency = 1
descRainbow.Text = "Quando ativado, seu carro vai mudar de cor automaticamente!"
descRainbow.TextColor3 = Color3.fromRGB(200,200,200)
descRainbow.TextStrokeTransparency = 0.8
descRainbow.Font = Enum.Font.SourceSans
descRainbow.TextSize = 18
descRainbow.TextWrapped = true
descRainbow.Parent = contentRainbow

-- Conteúdo central para Músicas
local contentMusic = Instance.new("Frame")
contentMusic.Name = "ContentMusic"
contentMusic.Size = UDim2.new(1, -144, 1, -46)
contentMusic.Position = UDim2.new(0, 144, 0, 46)
contentMusic.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
contentMusic.BackgroundTransparency = 0.10
contentMusic.BorderSizePixel = 0
contentMusic.Visible = false
contentMusic.Parent = mainFrame

-- Título
local musicTitle = Instance.new("TextLabel")
musicTitle.Size = UDim2.new(1, 0, 0, 50)
musicTitle.Position = UDim2.new(0, 0, 0, 0)
musicTitle.BackgroundTransparency = 1
musicTitle.Text = "Tocar Música"
musicTitle.Font = Enum.Font.GothamBold
musicTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
musicTitle.TextSize = 26
musicTitle.Parent = contentMusic

-- Caixa para colocar o ID da música
local musicBox = Instance.new("TextBox")
musicBox.Size = UDim2.new(0, 260, 0, 38)
musicBox.Position = UDim2.new(0.5, -130, 0, 65)
musicBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
musicBox.Text = ""
musicBox.PlaceholderText = "Cole o ID da música aqui"
musicBox.TextColor3 = Color3.fromRGB(255,255,255)
musicBox.Font = Enum.Font.SourceSans
musicBox.TextSize = 20
musicBox.BorderSizePixel = 0
musicBox.ClearTextOnFocus = false
musicBox.Parent = contentMusic

-- Botão para tocar/parar
local playBtn = Instance.new("TextButton")
playBtn.Size = UDim2.new(0, 120, 0, 38)
playBtn.Position = UDim2.new(0.5, -60, 0, 115)
playBtn.BackgroundColor3 = Color3.fromRGB(80, 180, 80)
playBtn.TextColor3 = Color3.fromRGB(255,255,255)
playBtn.Font = Enum.Font.SourceSansBold
playBtn.Text = "Ouvir"
playBtn.TextSize = 22
playBtn.BorderSizePixel = 0
playBtn.Parent = contentMusic

-- Mensagem de status
local musicStatus = Instance.new("TextLabel")
musicStatus.Size = UDim2.new(1, 0, 0, 32)
musicStatus.Position = UDim2.new(0, 0, 0, 165)
musicStatus.BackgroundTransparency = 1
musicStatus.Text = ""
musicStatus.TextColor3 = Color3.fromRGB(200,200,200)
musicStatus.Font = Enum.Font.SourceSans
musicStatus.TextSize = 17
musicStatus.TextWrapped = true
musicStatus.Parent = contentMusic

-- Alternar abas
menuBtn.MouseButton1Click:Connect(function()
    contentRainbow.Visible = true
    contentMusic.Visible = false
    menuBtn.TextColor3 = Color3.fromRGB(255,255,255)
    musicBtn.TextColor3 = Color3.fromRGB(160,160,160)
end)
musicBtn.MouseButton1Click:Connect(function()
    contentRainbow.Visible = false
    contentMusic.Visible = true
    menuBtn.TextColor3 = Color3.fromRGB(160,160,160)
    musicBtn.TextColor3 = Color3.fromRGB(255,255,255)
end)
-- Iniciar com "Menu" selecionado
menuBtn.TextColor3 = Color3.fromRGB(255,255,255)
musicBtn.TextColor3 = Color3.fromRGB(160,160,160)

-- Função: tocar música
local function stopMusic()
    if soundObj then
        soundObj:Stop()
        soundObj:Destroy()
        soundObj = nil
    end
end

playBtn.MouseButton1Click:Connect(function()
    if soundObj then
        stopMusic()
        playBtn.Text = "Ouvir"
        playBtn.BackgroundColor3 = Color3.fromRGB(80, 180, 80)
        musicStatus.Text = "Música parada."
        return
    end
    local id = tonumber(musicBox.Text)
    if not id then
        musicStatus.Text = "ID inválido!"
        return
    end
    stopMusic()
    soundObj = Instance.new("Sound")
    soundObj.SoundId = "rbxassetid://"..id
    soundObj.Volume = 3
    soundObj.Looped = true
    soundObj.Parent = workspace
    local ok, err = pcall(function() soundObj:Play() end)
    if ok then
        playBtn.Text = "Parar"
        playBtn.BackgroundColor3 = Color3.fromRGB(180, 60, 60)
        musicStatus.Text = "Tocando música ID: "..id
    else
        musicStatus.Text = "Erro ao tocar: "..tostring(err)
        stopMusic()
    end
end)

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
