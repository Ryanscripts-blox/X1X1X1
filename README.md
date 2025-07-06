-- X1X1X1 Hub Script
-- Criado para uso local no Roblox

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Variáveis do Hub
local hubVisible = true
local currentCategory = "Main"
local isMinimized = false

-- Funções ativas
local bringMobEnabled = false
local whiteScreenEnabled = false
local espEnabled = false
local godModeEnabled = false
local infiniteJumpEnabled = false
local noClipEnabled = false

-- Configurações
local selectedBoss = "None"

-- Criar GUI Principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "X1X1X1 HUB"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false

-- Frame Principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 800, 0, 500)
mainFrame.Position = UDim2.new(0.5, -400, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- Adicionar cantos arredondados
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

-- Barra de título
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 40)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 8)
titleCorner.Parent = titleBar

-- Título
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "Title"
titleLabel.Size = UDim2.new(1, -80, 1, 0)
titleLabel.Position = UDim2.new(0, 15, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "X1X1X1 HUB"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 18
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = titleBar

-- Botões de controle
local function createControlButton(name, position, text, color)
    local button = Instance.new("TextButton")
    button.Name = name
    button.Size = UDim2.new(0, 30, 0, 25)
    button.Position = position
    button.BackgroundColor3 = color
    button.BorderSizePixel = 0
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 14
    button.Font = Enum.Font.GothamBold
    button.Parent = titleBar
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 4)
    buttonCorner.Parent = button
    
    return button
end

local minimizeBtn = createControlButton("Minimize", UDim2.new(1, -70, 0, 7), "−", Color3.fromRGB(100, 100, 100))
local closeBtn = createControlButton("Close", UDim2.new(1, -35, 0, 7), "×", Color3.fromRGB(220, 53, 69))

-- Container principal
local contentContainer = Instance.new("Frame")
contentContainer.Name = "ContentContainer"
contentContainer.Size = UDim2.new(1, 0, 1, -40)
contentContainer.Position = UDim2.new(0, 0, 0, 40)
contentContainer.BackgroundTransparency = 1
contentContainer.Parent = mainFrame

-- Menu lateral (Sidebar)
local sidebar = Instance.new("Frame")
sidebar.Name = "Sidebar"
sidebar.Size = UDim2.new(0, 200, 1, 0)
sidebar.Position = UDim2.new(0, 0, 0, 0)
sidebar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
sidebar.BorderSizePixel = 0
sidebar.Parent = contentContainer

local sidebarCorner = Instance.new("UICorner")
sidebarCorner.CornerRadius = UDim.new(0, 8)
sidebarCorner.Parent = sidebar

-- Layout do sidebar
local sidebarLayout = Instance.new("UIListLayout")
sidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder
sidebarLayout.Padding = UDim.new(0, 2)
sidebarLayout.Parent = sidebar

-- Área de conteúdo principal
local mainContent = Instance.new("Frame")
mainContent.Name = "MainContent"
mainContent.Size = UDim2.new(1, -210, 1, 0)
mainContent.Position = UDim2.new(0, 210, 0, 0)
mainContent.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainContent.BorderSizePixel = 0
mainContent.Parent = contentContainer

local contentCorner = Instance.new("UICorner")
contentCorner.CornerRadius = UDim.new(0, 8)
contentCorner.Parent = mainContent

-- ScrollingFrame para o conteúdo
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Name = "ScrollFrame"
scrollFrame.Size = UDim2.new(1, -20, 1, -20)
scrollFrame.Position = UDim2.new(0, 10, 0, 10)
scrollFrame.BackgroundTransparency = 1
scrollFrame.BorderSizePixel = 0
scrollFrame.ScrollBarThickness = 6
scrollFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
scrollFrame.Parent = mainContent

-- Layout para os itens do conteúdo
local contentLayout = Instance.new("UIListLayout")
contentLayout.SortOrder = Enum.SortOrder.LayoutOrder
contentLayout.Padding = UDim.new(0, 8)
contentLayout.Parent = scrollFrame

-- Função para criar botões do sidebar
local function createSidebarButton(name, text, icon, order)
    local button = Instance.new("TextButton")
    button.Name = name
    button.Size = UDim2.new(1, -10, 0, 40)
    button.Position = UDim2.new(0, 5, 0, 0)
    button.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    button.BorderSizePixel = 0
    button.Text = ""
    button.LayoutOrder = order
    button.Parent = sidebar
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 6)
    buttonCorner.Parent = button
    
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Name = "Icon"
    iconLabel.Size = UDim2.new(0, 20, 0, 20)
    iconLabel.Position = UDim2.new(0, 15, 0, 10)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = icon
    iconLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
    iconLabel.TextSize = 14
    iconLabel.Font = Enum.Font.Gotham
    iconLabel.Parent = button
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Name = "Text"
    textLabel.Size = UDim2.new(1, -50, 1, 0)
    textLabel.Position = UDim2.new(0, 40, 0, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    textLabel.TextSize = 14
    textLabel.Font = Enum.Font.Gotham
    textLabel.TextXAlignment = Enum.TextXAlignment.Left
    textLabel.Parent = button
    
    return button
end

-- Função para criar toggle buttons
local function createToggleButton(name, text, callback, parent)
    local container = Instance.new("Frame")
    container.Name = name .. "Container"
    container.Size = UDim2.new(1, 0, 0, 40)
    container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    container.BorderSizePixel = 0
    container.Parent = parent or scrollFrame
    
    local containerCorner = Instance.new("UICorner")
    containerCorner.CornerRadius = UDim.new(0, 6)
    containerCorner.Parent = container
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Name = "Text"
    textLabel.Size = UDim2.new(1, -80, 1, 0)
    textLabel.Position = UDim2.new(0, 15, 0, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextSize = 14
    textLabel.Font = Enum.Font.Gotham
    textLabel.TextXAlignment = Enum.TextXAlignment.Left
    textLabel.Parent = container
    
    local toggleButton = Instance.new("TextButton")
    toggleButton.Name = "Toggle"
    toggleButton.Size = UDim2.new(0, 50, 0, 25)
    toggleButton.Position = UDim2.new(1, -65, 0, 7)
    toggleButton.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    toggleButton.BorderSizePixel = 0
    toggleButton.Text = ""
    toggleButton.Parent = container
    
    local toggleCorner = Instance.new("UICorner")
    toggleCorner.CornerRadius = UDim.new(0, 12)
    toggleCorner.Parent = toggleButton
    
    local toggleIndicator = Instance.new("Frame")
    toggleIndicator.Name = "Indicator"
    toggleIndicator.Size = UDim2.new(0, 21, 0, 21)
    toggleIndicator.Position = UDim2.new(0, 2, 0, 2)
    toggleIndicator.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
    toggleIndicator.BorderSizePixel = 0
    toggleIndicator.Parent = toggleButton
    
    local indicatorCorner = Instance.new("UICorner")
    indicatorCorner.CornerRadius = UDim.new(0, 10)
    indicatorCorner.Parent = toggleIndicator
    
    local isActive = false
    
    toggleButton.MouseButton1Click:Connect(function()
        isActive = not isActive
        
        local tweenInfo = TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        
        if isActive then
            local tween1 = TweenService:Create(toggleButton, tweenInfo, {BackgroundColor3 = Color3.fromRGB(52, 152, 219)})
            local tween2 = TweenService:Create(toggleIndicator, tweenInfo, {Position = UDim2.new(0, 27, 0, 2)})
            tween1:Play()
            tween2:Play()
        else
            local tween1 = TweenService:Create(toggleButton, tweenInfo, {BackgroundColor3 = Color3.fromRGB(70, 70, 70)})
            local tween2 = TweenService:Create(toggleIndicator, tweenInfo, {Position = UDim2.new(0, 2, 0, 2)})
            tween1:Play()
            tween2:Play()
        end
        
        if callback then
            callback(isActive)
        end
    end)
    
    return container
end

-- Função para criar dropdown
local function createDropdown(name, text, options, callback, parent)
    local container = Instance.new("Frame")
    container.Name = name .. "Container"
    container.Size = UDim2.new(1, 0, 0, 40)
    container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    container.BorderSizePixel = 0
    container.Parent = parent or scrollFrame
    
    local containerCorner = Instance.new("UICorner")
    containerCorner.CornerRadius = UDim.new(0, 6)
    containerCorner.Parent = container
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Name = "Text"
    textLabel.Size = UDim2.new(0.6, 0, 1, 0)
    textLabel.Position = UDim2.new(0, 15, 0, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextSize = 14
    textLabel.Font = Enum.Font.Gotham
    textLabel.TextXAlignment = Enum.TextXAlignment.Left
    textLabel.Parent = container
    
    local dropdown = Instance.new("TextButton")
    dropdown.Name = "Dropdown"
    dropdown.Size = UDim2.new(0.35, -15, 0, 25)
    dropdown.Position = UDim2.new(0.65, 0, 0, 7)
    dropdown.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    dropdown.BorderSizePixel = 0
    dropdown.Text = options[1] or "None"
    dropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
    dropdown.TextSize = 12
    dropdown.Font = Enum.Font.Gotham
    dropdown.Parent = container
    
    local dropdownCorner = Instance.new("UICorner")
    dropdownCorner.CornerRadius = UDim.new(0, 4)
    dropdownCorner.Parent = dropdown
    
    return container
end

-- Criar botões do sidebar
local mainBtn = createSidebarButton("Main", "Main", "◈", 1)

-- Funções do jogo
local function toggleBringMob()
    bringMobEnabled = not bringMobEnabled
    if bringMobEnabled then
        spawn(function()
            while bringMobEnabled do
                -- Lógica para trazer mobs
                for _, mob in pairs(Workspace:GetChildren()) do
                    if mob:FindFirstChild("Humanoid") and mob:FindFirstChild("HumanoidRootPart") and mob ~= player.Character then
                        local distance = (player.Character.HumanoidRootPart.Position - mob.HumanoidRootPart.Position).Magnitude
                        if distance > 20 then
                            mob.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame + Vector3.new(0, 0, -10)
                        end
                    end
                end
                wait(0.5)
            end
        end)
    end
end

local function toggleAutoFarm()
    -- Função removida
end

local function toggleInfiniteJump()
    infiniteJumpEnabled = not infiniteJumpEnabled
    if infiniteJumpEnabled then
        local connection
        connection = UserInputService.JumpRequest:Connect(function()
            if infiniteJumpEnabled and player.Character and player.Character:FindFirstChild("Humanoid") then
                player.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
        
        player.Character:SetAttribute("InfiniteJumpConnection", connection)
    else
        local connection = player.Character:GetAttribute("InfiniteJumpConnection")
        if connection then
            connection:Disconnect()
        end
    end
end

local function toggleWhiteScreen()
    whiteScreenEnabled = not whiteScreenEnabled
    if whiteScreenEnabled then
        local whiteScreen = Instance.new("Frame")
        whiteScreen.Name = "WhiteScreen"
        whiteScreen.Size = UDim2.new(1, 0, 1, 0)
        whiteScreen.Position = UDim2.new(0, 0, 0, 0)
        whiteScreen.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        whiteScreen.BorderSizePixel = 0
        whiteScreen.Parent = screenGui
        whiteScreen.ZIndex = 1000
    else
        local whiteScreen = screenGui:FindFirstChild("WhiteScreen")
        if whiteScreen then
            whiteScreen:Destroy()
        end
    end
end

local function toggleGodMode()
    godModeEnabled = not godModeEnabled
    if godModeEnabled then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.MaxHealth = math.huge
            humanoid.Health = math.huge
            
            local connection
            connection = humanoid.HealthChanged:Connect(function()
                if godModeEnabled then
                    humanoid.Health = math.huge
                end
            end)
            
            player.Character:SetAttribute("GodModeConnection", connection)
        end
    else
        local connection = player.Character:GetAttribute("GodModeConnection")
        if connection then
            connection:Disconnect()
        end
        
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.MaxHealth = 100
            humanoid.Health = 100
        end
    end
end

local function toggleNoClip()
    noClipEnabled = not noClipEnabled
    if noClipEnabled then
        spawn(function()
            while noClipEnabled do
                for _, part in pairs(player.Character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
                wait(0.1)
            end
        end)
    else
        for _, part in pairs(player.Character:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.CanCollide = true
            end
        end
    end
end

-- Função para limpar e criar conteúdo
local function createContent(category)
    for _, child in pairs(scrollFrame:GetChildren()) do
        if child:IsA("Frame") or child:IsA("TextLabel") then
            child:Destroy()
        end
    end
    
    if category == "Main" then
        createToggleButton("BringMob", "Bring Mob", function(state) toggleBringMob() end)
        createToggleButton("InfiniteJump", "Infinite Jump", function(state) toggleInfiniteJump() end)
        createToggleButton("GodMode", "God Mode", function(state) toggleGodMode() end)
        createToggleButton("NoClip", "No Clip", function(state) toggleNoClip() end)
        createToggleButton("WhiteScreen", "White Screen", function(state) toggleWhiteScreen() end)
    end
    
    -- Atualizar tamanho do scroll
    wait(0.1)
    local contentSize = contentLayout.AbsoluteContentSize
    scrollFrame.CanvasSize = UDim2.new(0, 0, 0, contentSize.Y + 20)
end

-- Função para destacar botão ativo
local function highlightButton(activeButton)
    for _, button in pairs(sidebar:GetChildren()) do
        if button:IsA("TextButton") then
            button.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            local icon = button:FindFirstChild("Icon")
            local text = button:FindFirstChild("Text")
            if icon then icon.TextColor3 = Color3.fromRGB(150, 150, 150) end
            if text then text.TextColor3 = Color3.fromRGB(200, 200, 200) end
        end
    end
    
    activeButton.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
    local icon = activeButton:FindFirstChild("Icon")
    local text = activeButton:FindFirstChild("Text")
    if icon then icon.TextColor3 = Color3.fromRGB(255, 255, 255) end
    if text then text.TextColor3 = Color3.fromRGB(255, 255, 255) end
end

-- Conectar botões do sidebar
mainBtn.MouseButton1Click:Connect(function()
    currentCategory = "Main"
    highlightButton(mainBtn)
    createContent("Main")
end)

-- Funções dos botões de controle
minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        local tween = TweenService:Create(mainFrame, tweenInfo, {
            Size = UDim2.new(0, 800, 0, 40)
        })
        tween:Play()
        contentContainer.Visible = false
        minimizeBtn.Text = "+"
    else
        local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        local tween = TweenService:Create(mainFrame, tweenInfo, {
            Size = UDim2.new(0, 800, 0, 500)
        })
        tween:Play()
        contentContainer.Visible = true
        minimizeBtn.Text = "−"
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Tornar o hub arrastável
local dragging = false
local dragStart = nil
local startPos = nil

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

titleBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

titleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Inicializar com categoria Main
highlightButton(mainBtn)
createContent("Main")

-- Mostrar notificação de carregamento
local function showNotification(text)
    local notification = Instance.new("Frame")
    notification.Size = UDim2.new(0, 300, 0, 60)
    notification.Position = UDim2.new(1, -320, 0, 20)
    notification.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    notification.BorderSizePixel = 0
    notification.Parent = screenGui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 8)
    notifCorner.Parent = notification
    
    local notifText = Instance.new("TextLabel")
    notifText.Size = UDim2.new(1, -20, 1, 0)
    notifText.Position = UDim2.new(0, 10, 0, 0)
    notifText.BackgroundTransparency = 1
    notifText.Text = text
    notifText.TextColor3 = Color3.fromRGB(255, 255, 255)
    notifText.TextScaled = true
    notifText.Font = Enum.Font.Gotham
    notifText.Parent = notification
    
    local tweenIn = TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(1, -320, 0, 20)})
    tweenIn:Play()
    
    wait(3)
    local tweenOut = TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(1, 0, 0, 20)})
    tweenOut:Play()
    tweenOut.Completed:Connect(function()
        notification:Destroy()
    end)
end

spawn(function()
    showNotification("Xero Hub loaded successfully!")
end)

print("Xero Hub iniciado com sucesso!")
print("Criado por: Xero Development Team")
print("Versão: 1.0")
print("Design inspirado em interfaces modernas")
