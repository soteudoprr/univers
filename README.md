-- Script Menu Completo para Roblox
-- Todas as funcionalidades incluídas

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local GuiService = game:GetService("GuiService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Variáveis de estado
local menuOpen = false
local flyEnabled = false
local noClipEnabled = false
local espEnabled = false
local fullbrightEnabled = false
local infiniteJumpEnabled = false
local speedBoostEnabled = false
local godModeEnabled = false
local isMobile = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled

-- Configurações
local defaultWalkSpeed = 16
local defaultJumpPower = 50
local flySpeed = 50
local speedMultiplier = 2

-- Criação da GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MenuScript"
screenGui.Parent = playerGui

-- Frame principal do menu
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 400, 0, 500)
mainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Parent = screenGui

-- Adicionar cantos arredondados
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = mainFrame

-- Título do menu
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "Title"
titleLabel.Size = UDim2.new(1, 0, 0, 40)
titleLabel.Position = UDim2.new(0, 0, 0, 0)
titleLabel.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
titleLabel.BorderSizePixel = 0
titleLabel.Text = "🎮 MENU SCRIPT ROBLOX"
titleLabel.TextColor3 = Color3.new(1, 1, 1)
titleLabel.TextScaled = true
titleLabel.Font = Enum.Font.GothamBold
titleLabel.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 10)
titleCorner.Parent = titleLabel

-- ScrollFrame para os botões
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Name = "ScrollFrame"
scrollFrame.Size = UDim2.new(1, -20, 1, -60)
scrollFrame.Position = UDim2.new(0, 10, 0, 50)
scrollFrame.BackgroundTransparency = 1
scrollFrame.BorderSizePixel = 0
scrollFrame.ScrollBarThickness = 8
scrollFrame.Parent = mainFrame

-- Layout para organizar os botões
local listLayout = Instance.new("UIListLayout")
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout.Padding = UDim.new(0, 5)
listLayout.Parent = scrollFrame

-- Botão de fechar
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
closeButton.BorderSizePixel = 0
closeButton.Text = "X"
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.TextScaled = true
closeButton.Font = Enum.Font.GothamBold
closeButton.Parent = mainFrame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 5)
closeCorner.Parent = closeButton

-- Botão flutuante para mobile
local floatingButton
if isMobile then
    floatingButton = Instance.new("TextButton")
    floatingButton.Name = "FloatingButton"
    floatingButton.Size = UDim2.new(0, 60, 0, 60)
    floatingButton.Position = UDim2.new(1, -80, 0.5, -30)
    floatingButton.BackgroundColor3 = Color3.new(0.2, 0.4, 0.8)
    floatingButton.BorderSizePixel = 0
    floatingButton.Text = "🎮"
    floatingButton.TextColor3 = Color3.new(1, 1, 1)
    floatingButton.TextScaled = true
    floatingButton.Font = Enum.Font.GothamBold
    floatingButton.ZIndex = 10
    floatingButton.Parent = screenGui
    
    -- Fazer o botão redondo
    local floatingCorner = Instance.new("UICorner")
    floatingCorner.CornerRadius = UDim.new(0.5, 0)
    floatingCorner.Parent = floatingButton
    
    -- Adicionar sombra/borda
    local floatingStroke = Instance.new("UIStroke")
    floatingStroke.Color = Color3.new(0, 0, 0)
    floatingStroke.Thickness = 2
    floatingStroke.Transparency = 0.5
    floatingStroke.Parent = floatingButton
    
    -- Tornar o botão arrastável
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    floatingButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = floatingButton.Position
        end
    end)
    
    floatingButton.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            floatingButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    
    floatingButton.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            if dragging then
                dragging = false
                -- Se não arrastou muito, considera como clique
                local delta = input.Position - dragStart
                if delta.Magnitude < 10 then
                    toggleMenu()
                end
            end
        end
    end)
    
    -- Efeito de hover/press para mobile
    floatingButton.MouseButton1Down:Connect(function()
        TweenService:Create(floatingButton, TweenInfo.new(0.1), {Size = UDim2.new(0, 55, 0, 55)}):Play()
    end)
    
    floatingButton.MouseButton1Up:Connect(function()
        TweenService:Create(floatingButton, TweenInfo.new(0.1), {Size = UDim2.new(0, 60, 0, 60)}):Play()
    end)
    
    -- Animação de pulso sutil
    spawn(function()
        while floatingButton and floatingButton.Parent do
            TweenService:Create(floatingButton, TweenInfo.new(2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
                BackgroundColor3 = Color3.new(0.3, 0.5, 0.9)
            }):Play()
            wait(2)
        end
    end)
end

-- Função para criar botões
local function createButton(text, callback, color)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -10, 0, 40)
    button.BackgroundColor3 = color or Color3.new(0.2, 0.4, 0.8)
    button.BorderSizePixel = 0
    button.Text = text
    button.TextColor3 = Color3.new(1, 1, 1)
    button.TextScaled = true
    button.Font = Enum.Font.Gotham
    button.Parent = scrollFrame
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 5)
    buttonCorner.Parent = button
    
    button.MouseButton1Click:Connect(callback)
    
    -- Efeito hover
    button.MouseEnter:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = Color3.new(0.3, 0.5, 0.9)}):Play()
    end)
    
    button.MouseLeave:Connect(function()
        TweenService:Create(button, TweenInfo.new(0.2), {BackgroundColor3 = color or Color3.new(0.2, 0.4, 0.8)}):Play()
    end)
    
    return button
end

-- Função para mostrar notificação
local function showNotification(text, color)
    local notification = Instance.new("Frame")
    notification.Size = UDim2.new(0, 300, 0, 60)
    notification.Position = UDim2.new(1, -320, 0, 20)
    notification.BackgroundColor3 = color or Color3.new(0.2, 0.8, 0.2)
    notification.BorderSizePixel = 0
    notification.Parent = screenGui
    
    local notifCorner = Instance.new("UICorner")
    notifCorner.CornerRadius = UDim.new(0, 8)
    notifCorner.Parent = notification
    
    local notifText = Instance.new("TextLabel")
    notifText.Size = UDim2.new(1, -10, 1, 0)
    notifText.Position = UDim2.new(0, 5, 0, 0)
    notifText.BackgroundTransparency = 1
    notifText.Text = text
    notifText.TextColor3 = Color3.new(1, 1, 1)
    notifText.TextScaled = true
    notifText.Font = Enum.Font.Gotham
    notifText.Parent = notification
    
    -- Animação de entrada
    notification.Position = UDim2.new(1, 0, 0, 20)
    TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(1, -320, 0, 20)}):Play()
    
    -- Remove após 3 segundos
    wait(3)
    TweenService:Create(notification, TweenInfo.new(0.3), {Position = UDim2.new(1, 0, 0, 20)}):Play()
    wait(0.3)
    notification:Destroy()
end

-- Funcionalidades

-- Fly
local flyConnection
local function toggleFly()
    flyEnabled = not flyEnabled
    
    if flyEnabled then
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.Parent = character.HumanoidRootPart
        
        flyConnection = RunService.Heartbeat:Connect(function()
            local camera = workspace.CurrentCamera
            local moveVector = humanoid.MoveDirection
            local lookVector = camera.CFrame.LookVector
            local rightVector = camera.CFrame.RightVector
            
            local velocity = Vector3.new(0, 0, 0)
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                velocity = velocity + lookVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                velocity = velocity - lookVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                velocity = velocity - rightVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                velocity = velocity + rightVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                velocity = velocity + Vector3.new(0, 1, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                velocity = velocity - Vector3.new(0, 1, 0)
            end
            
            bodyVelocity.Velocity = velocity * flySpeed
        end)
        
        showNotification("✈️ Fly ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        if flyConnection then
            flyConnection:Disconnect()
        end
        if character.HumanoidRootPart:FindFirstChild("BodyVelocity") then
            character.HumanoidRootPart.BodyVelocity:Destroy()
        end
        showNotification("✈️ Fly DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- NoClip
local noClipConnection
local function toggleNoClip()
    noClipEnabled = not noClipEnabled
    
    if noClipEnabled then
        noClipConnection = RunService.Stepped:Connect(function()
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") and part.CanCollide then
                    part.CanCollide = false
                end
            end
        end)
        showNotification("🚫 NoClip ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        if noClipConnection then
            noClipConnection:Disconnect()
        end
        for _, part in pairs(character:GetChildren()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
        showNotification("🚫 NoClip DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- ESP (Mostrar jogadores através das paredes)
local espConnections = {}
local function toggleESP()
    espEnabled = not espEnabled
    
    if espEnabled then
        for _, otherPlayer in pairs(Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local highlight = Instance.new("Highlight")
                highlight.Parent = otherPlayer.Character
                highlight.FillColor = Color3.new(1, 0, 0)
                highlight.OutlineColor = Color3.new(1, 1, 1)
                espConnections[otherPlayer] = highlight
            end
        end
        showNotification("👁️ ESP ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        for _, highlight in pairs(espConnections) do
            if highlight then
                highlight:Destroy()
            end
        end
        espConnections = {}
        showNotification("👁️ ESP DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- Fullbright
local originalAmbient
local originalBrightness
local function toggleFullbright()
    fullbrightEnabled = not fullbrightEnabled
    
    if fullbrightEnabled then
        originalAmbient = Lighting.Ambient
        originalBrightness = Lighting.Brightness
        Lighting.Ambient = Color3.new(1, 1, 1)
        Lighting.Brightness = 2
        showNotification("💡 Fullbright ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        Lighting.Ambient = originalAmbient or Color3.new(0, 0, 0)
        Lighting.Brightness = originalBrightness or 1
        showNotification("💡 Fullbright DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- Speed Boost
local function toggleSpeedBoost()
    speedBoostEnabled = not speedBoostEnabled
    
    if speedBoostEnabled then
        humanoid.WalkSpeed = defaultWalkSpeed * speedMultiplier
        showNotification("🏃 Speed Boost ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        humanoid.WalkSpeed = defaultWalkSpeed
        showNotification("🏃 Speed Boost DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- Infinite Jump
local function toggleInfiniteJump()
    infiniteJumpEnabled = not infiniteJumpEnabled
    
    if infiniteJumpEnabled then
        UserInputService.JumpRequest:Connect(function()
            if infiniteJumpEnabled then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
        showNotification("🦘 Infinite Jump ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        showNotification("🦘 Infinite Jump DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- God Mode
local function toggleGodMode()
    godModeEnabled = not godModeEnabled
    
    if godModeEnabled then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
        showNotification("⚡ God Mode ATIVADO", Color3.new(0.2, 0.8, 0.2))
    else
        humanoid.MaxHealth = 100
        humanoid.Health = 100
        showNotification("⚡ God Mode DESATIVADO", Color3.new(0.8, 0.2, 0.2))
    end
end

-- Teleport para jogadores
local function createTeleportMenu()
    local teleportFrame = Instance.new("Frame")
    teleportFrame.Size = UDim2.new(0, 300, 0, 400)
    teleportFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
    teleportFrame.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
    teleportFrame.BorderSizePixel = 0
    teleportFrame.Parent = screenGui
    
    local tpCorner = Instance.new("UICorner")
    tpCorner.CornerRadius = UDim.new(0, 10)
    tpCorner.Parent = teleportFrame
    
    local tpTitle = Instance.new("TextLabel")
    tpTitle.Size = UDim2.new(1, 0, 0, 40)
    tpTitle.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    tpTitle.Text = "📍 TELEPORT MENU"
    tpTitle.TextColor3 = Color3.new(1, 1, 1)
    tpTitle.TextScaled = true
    tpTitle.Font = Enum.Font.GothamBold
    tpTitle.Parent = teleportFrame
    
    local tpTitleCorner = Instance.new("UICorner")
    tpTitleCorner.CornerRadius = UDim.new(0, 10)
    tpTitleCorner.Parent = tpTitle
    
    local tpScroll = Instance.new("ScrollingFrame")
    tpScroll.Size = UDim2.new(1, -20, 1, -80)
    tpScroll.Position = UDim2.new(0, 10, 0, 50)
    tpScroll.BackgroundTransparency = 1
    tpScroll.Parent = teleportFrame
    
    local tpLayout = Instance.new("UIListLayout")
    tpLayout.SortOrder = Enum.SortOrder.LayoutOrder
    tpLayout.Padding = UDim.new(0, 5)
    tpLayout.Parent = tpScroll
    
    for _, otherPlayer in pairs(Players:GetPlayers()) do
        if otherPlayer ~= player then
            local tpButton = Instance.new("TextButton")
            tpButton.Size = UDim2.new(1, -10, 0, 35)
            tpButton.BackgroundColor3 = Color3.new(0.3, 0.6, 0.3)
            tpButton.Text = "📍 " .. otherPlayer.Name
            tpButton.TextColor3 = Color3.new(1, 1, 1)
            tpButton.TextScaled = true
            tpButton.Font = Enum.Font.Gotham
            tpButton.Parent = tpScroll
            
            local tpBtnCorner = Instance.new("UICorner")
            tpBtnCorner.CornerRadius = UDim.new(0, 5)
            tpBtnCorner.Parent = tpButton
            
            tpButton.MouseButton1Click:Connect(function()
                if otherPlayer.Character and otherPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    character.HumanoidRootPart.CFrame = otherPlayer.Character.HumanoidRootPart.CFrame
                    showNotification("📍 Teleportado para " .. otherPlayer.Name, Color3.new(0.2, 0.8, 0.2))
                    teleportFrame:Destroy()
                end
            end)
        end
    end
    
    local closeTpButton = Instance.new("TextButton")
    closeTpButton.Size = UDim2.new(1, -20, 0, 30)
    closeTpButton.Position = UDim2.new(0, 10, 1, -40)
    closeTpButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
    closeTpButton.Text = "❌ FECHAR"
    closeTpButton.TextColor3 = Color3.new(1, 1, 1)
    closeTpButton.TextScaled = true
    closeTpButton.Font = Enum.Font.Gotham
    closeTpButton.Parent = teleportFrame
    
    local closeTpCorner = Instance.new("UICorner")
    closeTpCorner.CornerRadius = UDim.new(0, 5)
    closeTpCorner.Parent = closeTpButton
    
    closeTpButton.MouseButton1Click:Connect(function()
        teleportFrame:Destroy()
    end)
end

-- Criação dos botões do menu
createButton("✈️ Fly", toggleFly)
createButton("🚫 NoClip", toggleNoClip)
createButton("👁️ ESP Players", toggleESP)
createButton("💡 Fullbright", toggleFullbright)
createButton("🏃 Speed Boost", toggleSpeedBoost)
createButton("🦘 Infinite Jump", toggleInfiniteJump)
createButton("⚡ God Mode", toggleGodMode)
createButton("📍 Teleport Menu", createTeleportMenu, Color3.new(0.6, 0.3, 0.8))

-- Ajustar o tamanho do ScrollFrame
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y)
listLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    scrollFrame.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y)
end)

-- Função para alternar o menu
local function toggleMenu()
    menuOpen = not menuOpen
    mainFrame.Visible = menuOpen
    
    if menuOpen then
        -- Animação de abertura
        mainFrame.Size = UDim2.new(0, 0, 0, 0)
        mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
        TweenService:Create(mainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back), {
            Size = UDim2.new(0, 400, 0, 500),
            Position = UDim2.new(0.5, -200, 0.5, -250)
        }):Play()
    end
end

-- Eventos
closeButton.MouseButton1Click:Connect(toggleMenu)

-- Tecla para abrir/fechar o menu (Right Shift) - apenas para PC
if not isMobile then
    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if not gameProcessed and input.KeyCode == Enum.KeyCode.RightShift then
            toggleMenu()
        end
    end)
end

-- Atualizar personagem quando respawnar
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    
    -- Reativar funcionalidades se estavam ativas
    if flyEnabled then
        flyEnabled = false
        toggleFly()
    end
    if noClipEnabled then
        noClipEnabled = false
        toggleNoClip()
    end
    if speedBoostEnabled then
        speedBoostEnabled = false
        toggleSpeedBoost()
    end
    if godModeEnabled then
        godModeEnabled = false
        toggleGodMode()
    end
end)

-- Mostrar notificação inicial
if isMobile then
    showNotification("🎮 Menu carregado! Toque na bolinha flutuante para abrir", Color3.new(0.1, 0.1, 0.1))
    print("Menu Script carregado com sucesso!")
    print("Toque na bolinha flutuante para abrir o menu")
else
    showNotification("🎮 Menu carregado! Pressione SHIFT DIREITO para abrir", Color3.new(0.1, 0.1, 0.1))
    print("Menu Script carregado com sucesso!")
    print("Pressione SHIFT DIREITO para abrir o menu")
end
        flyEnabled = false
        toggleFly()
    end
    if noClipEnabled then
        noClipEnabled = false
        toggleNoClip()
    end
    if speedBoostEnabled then
        speedBoostEnabled = false
        toggleSpeedBoost()
    end
    if godModeEnabled then
        godModeEnabled = false
        toggleGodMode()
    end
end)

-- Mostrar notificação inicial
showNotification("🎮 Menu carregado! Pressione SHIFT DIREITO para abrir", Color3.new(0.1, 0.1, 0.1))

print("Menu Script carregado com sucesso!")
print("Pressione SHIFT DIREITO para abrir o menu")
