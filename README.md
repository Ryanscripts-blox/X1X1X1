-- Espera o jogo carregar para evitar crashes
task.wait(2)

-- Serviços e Variáveis Globais
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local LocalPlayer = Players.LocalPlayer

-- Variáveis de controle para as funções
local isFlying = false
local noclipEnabled = false
local infinityJumpEnabled = false
local skywalkEnabled = false
local skywalkPlatform = nil
local godModeConnection = nil

-- Carrega a interface Rayfield
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "X1X1X1 HUB",
   LoadingTitle = "carregando...",
   LoadingSubtitle = "by Ryan",
   ShowText = "X1X1X1 HUB",
   Theme = "Default",
   ToggleUIKeybind = "K",
   ConfigurationSaving = {
      Enabled = true,
      FileName = "X1X1X1HUBConfig"
   },
   KeySystem = false,
   KeySettings = {
      Title = "X1X1X1 HUB",
      Subtitle = "Sistema de Key",
      Note = "Obtenha a key no nosso servidor de Discord.",
      FileName = "KeyX1X1X1",
      SaveKey = true,
      Key = {"x1x1x1 key"}
   }
})

local Tab = Window:CreateTab("Main", 4483362458)
local Section = Tab:CreateSection("Funções Principais")

-- God Mode (Método Melhorado)
local GodModeToggle = Tab:CreateToggle({
   Name = "God Mode",
   CurrentValue = false,
   Flag = "GodMode",
   Callback = function(Value)
        local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if not humanoid then return end

        if Value then
            godModeConnection = humanoid.HealthChanged:Connect(function(health)
                if health < humanoid.MaxHealth then
                    humanoid.Health = humanoid.MaxHealth
                end
            end)
        else
            if godModeConnection then
                godModeConnection:Disconnect()
                godModeConnection = nil
            end
        end
   end,
})

-- Infinity Jump
local InfinityJumpToggle = Tab:CreateToggle({
    Name = "Infinity Jump",
    CurrentValue = false,
    Flag = "InfinityJump",
    Callback = function(Value)
        infinityJumpEnabled = Value
    end,
})

-- Sky Walk (ALTURA CORRIGIDA)
local SkyWalkToggle = Tab:CreateToggle({
   Name = "Sky Walk",
   CurrentValue = false,
   Flag = "SkyWalk",
   Callback = function(Value)
        skywalkEnabled = Value
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end

        if Value then
            local hrp = char.HumanoidRootPart
            -- ALTURA AJUSTADA PARA FICAR IGUAL A IMAGEM
            local SKY_ALTITUDE = 100 
            
            -- 1. Teleporta o jogador para o céu
            hrp.CFrame = CFrame.new(hrp.Position.X, hrp.Position.Y + SKY_ALTITUDE, hrp.Position.Z)

            task.spawn(function()
                -- 2. Cria a plataforma invisível
                skywalkPlatform = Instance.new("Part", workspace)
                skywalkPlatform.Name = "SkywalkPlatform_X1X1X1"
                skywalkPlatform.Anchored = true
                skywalkPlatform.CanCollide = true
                skywalkPlatform.Transparency = 1
                skywalkPlatform.Size = Vector3.new(20, 1, 20)
                
                -- Salva a altitude exata para manter o jogador nela
                local targetY = hrp.Position.Y - 4

                -- 3. Move a plataforma para seguir o jogador no céu
                while skywalkEnabled and skywalkPlatform do
                    local current_char = LocalPlayer.Character
                    if current_char and current_char:FindFirstChild("HumanoidRootPart") then
                        local rootPos = current_char.HumanoidRootPart.Position
                        -- Mantém a plataforma na altitude definida
                        skywalkPlatform.CFrame = CFrame.new(rootPos.X, targetY, rootPos.Z)
                    end
                    task.wait()
                end
            end)
        else
            -- Destrói a plataforma quando a função é desativada
            if skywalkPlatform then
                skywalkPlatform:Destroy()
                skywalkPlatform = nil
            end
        end
   end,
})

-- Speed
local SpeedSlider = Tab:CreateSlider({
   Name = "Velocidade",
   Range = {16, 500},
   CurrentValue = 16,
   Flag = "Speed",
   Callback = function(Value)
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end
   end,
})

-- Fly
local FlyToggle = Tab:CreateToggle({
   Name = "Fly",
   CurrentValue = false,
   Flag = "Fly",
   Callback = function(Value)
        isFlying = Value
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local hrp = char.HumanoidRootPart

        if Value then
            task.spawn(function()
                local bodyVelocity = Instance.new("BodyVelocity", hrp)
                bodyVelocity.Name = "FlyVelocity"
                bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                local bodyGyro = Instance.new("BodyGyro", hrp)
                bodyGyro.Name = "FlyGyro"
                bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                bodyGyro.P = 3000

                while isFlying do
                    local speed = 50
                    local velocity = Vector3.new(0, 0, 0)
                    if UserInputService:IsKeyDown(Enum.KeyCode.W) then velocity = velocity + workspace.CurrentCamera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.S) then velocity = velocity - workspace.CurrentCamera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.A) then velocity = velocity - workspace.CurrentCamera.CFrame.RightVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.D) then velocity = velocity + workspace.CurrentCamera.CFrame.RightVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then velocity = velocity + Vector3.new(0, 1, 0) end
                    if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then velocity = velocity - Vector3.new(0, 1, 0) end
                    
                    bodyVelocity.Velocity = velocity.Unit * speed
                    bodyGyro.CFrame = workspace.CurrentCamera.CFrame
                    task.wait()
                end
                
                if hrp:FindFirstChild("FlyVelocity") then hrp.FlyVelocity:Destroy() end
                if hrp:FindFirstChild("FlyGyro") then hrp.FlyGyro:Destroy() end
            end)
        end
   end,
})

-- Noclip
local NoclipToggle = Tab:CreateToggle({
   Name = "Noclip",
   CurrentValue = false,
   Flag = "Noclip",
   Callback = function(Value)
        noclipEnabled = Value
        task.spawn(function()
            while noclipEnabled do
                if LocalPlayer.Character then
                    for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
                task.wait(0.1)
            end
        end)
   end,
})

-- Auto Server Hop
local AutoServerHopButton = Tab:CreateButton({
   Name = "Auto Server Hop",
   Flag = "AutoServerHop",
   Callback = function()
        TeleportService:Teleport(game.PlaceId)
   end,
})

-- Lógica do Infinity Jump
UserInputService.JumpRequest:Connect(function()
    if infinityJumpEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- Limpeza ao fechar a GUI
game:GetService("CoreGui").DescendantRemoving:Connect(function(child)
    if child == Rayfield.Parent then
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = 16
        end
        if godModeConnection then godModeConnection:Disconnect() end
        if skywalkPlatform then skywalkPlatform:Destroy() end
        isFlying = false
        noclipEnabled = false
        infinityJumpEnabled = false
        skywalkEnabled = false
    end
end)
