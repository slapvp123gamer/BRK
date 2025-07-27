--[[
    ╔═══════════════════════════════════════════════════════════════════════════════════╗
    ║                          BaxiHub BRK Enhanced by: yuyu e imxddos_                 ║
    ║                                Brookhaven Script V2.0                             ║
    ║                              Versão 2.0 - Janeiro 2025                           ║
    ╚═══════════════════════════════════════════════════════════════════════════════════╝
]]

-- ═══════════════════════════════════════════════════════════════════════════════════
-- SERVIÇOS E VARIÁVEIS GLOBAIS
-- ═══════════════════════════════════════════════════════════════════════════════════

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local SoundService = game:GetService("SoundService")
local PathfindingService = game:GetService("PathfindingService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")
local Character = Player.Character or Player.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Estados dos hacks
local HackStates = {
    Speed = false,
    JumpPower = false,
    Fly = false,
    GodMode = false,
    Invisible = false,
    DoorsRemoved = false,
    InfiniteCars = false,
    FloatPlayers = false,
    Fireworks = false,
    RainbowMode = false,
    Disco = false,
    Earthquake = false,
    FakeChat = false,
    SpamParts = false,
    AntiGravity = false,
    TimeFreeze = false
}

-- Conexões ativas
local ActiveConnections = {}

-- ═══════════════════════════════════════════════════════════════════════════════════
-- FUNÇÕES UTILITÁRIAS MELHORADAS
-- ═══════════════════════════════════════════════════════════════════════════════════

-- Função para criar tweens suaves com múltiplos estilos
local function CreateTween(object, info, properties)
    local tween = TweenService:Create(object, info, properties)
    tween:Play()
    return tween
end

-- Sistema de notificação melhorado com ícones e sons
local function Notify(text, color, icon, duration)
    duration = duration or 3
    icon = icon or "🔔"
    
    local notification = Instance.new("ScreenGui")
    notification.Name = "BaxiNotification"
    notification.Parent = PlayerGui
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 60)
    frame.Position = UDim2.new(1, -370, 0, 20)
    frame.BackgroundColor3 = color or Color3.fromRGB(40, 40, 50)
    frame.BorderSizePixel = 0
    frame.Parent = notification
    
    -- Gradient para visual mais moderno
    local gradient = Instance.new("UIGradient")
    gradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, color or Color3.fromRGB(40, 40, 50)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(60, 60, 80))
    }
    gradient.Rotation = 45
    gradient.Parent = frame
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = frame
    
    -- Sombra
    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 4, 1, 4)
    shadow.Position = UDim2.new(0, -2, 0, 2)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    shadow.BackgroundTransparency = 0.8
    shadow.ZIndex = frame.ZIndex - 1
    shadow.Parent = notification
    
    local shadowCorner = Instance.new("UICorner")
    shadowCorner.CornerRadius = UDim.new(0, 12)
    shadowCorner.Parent = shadow
    
    -- Ícone
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Size = UDim2.new(0, 40, 1, 0)
    iconLabel.Position = UDim2.new(0, 10, 0, 0)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = icon
    iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    iconLabel.TextScaled = true
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.Parent = frame
    
    -- Texto principal
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -60, 1, 0)
    label.Position = UDim2.new(0, 50, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextScaled = true
    label.Font = Enum.Font.GothamBold
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    
    -- Barra de progresso
    local progressBar = Instance.new("Frame")
    progressBar.Size = UDim2.new(1, 0, 0, 3)
    progressBar.Position = UDim2.new(0, 0, 1, -3)
    progressBar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    progressBar.BorderSizePixel = 0
    progressBar.Parent = frame
    
    -- Animação de entrada
    frame.Position = UDim2.new(1, 0, 0, 20)
    CreateTween(frame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.new(1, -370, 0, 20)})
    
    -- Animação da barra de progresso
    CreateTween(progressBar, TweenInfo.new(duration, Enum.EasingStyle.Linear), {Size = UDim2.new(0, 0, 0, 3)})
    
    -- Remove após duração
    spawn(function()
        wait(duration)
        CreateTween(frame, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {Position = UDim2.new(1, 0, 0, 20)})
        wait(0.3)
        notification:Destroy()
    end)
end

-- Sistema de partículas
local function CreateParticleEffect(position, color, count)
    count = count or 10
    for i = 1, count do
        local particle = Instance.new("Part")
        particle.Name = "Particle"
        particle.Size = Vector3.new(0.5, 0.5, 0.5)
        particle.Material = Enum.Material.Neon
        particle.BrickColor = color or BrickColor.Random()
        particle.Shape = Enum.PartType.Ball
        particle.Position = position + Vector3.new(math.random(-5, 5), math.random(-2, 2), math.random(-5, 5))
        particle.CanCollide = false
        particle.Parent = Workspace
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        bodyVelocity.Velocity = Vector3.new(
            math.random(-20, 20),
            math.random(10, 30),
            math.random(-20, 20)
        )
        bodyVelocity.Parent = particle
        
        -- Fade out effect
        spawn(function()
            for transparency = 0, 1, 0.1 do
                particle.Transparency = transparency
                wait(0.1)
            end
            particle:Destroy()
        end)
        
        game:GetService("Debris"):AddItem(particle, 3)
    end
end

-- ═══════════════════════════════════════════════════════════════════════════════════
-- FUNÇÕES DOS HACKS - MAIN (MELHORADAS)
-- ═══════════════════════════════════════════════════════════════════════════════════

local function ToggleSpeed()
    HackStates.Speed = not HackStates.Speed
    if HackStates.Speed then
        Humanoid.WalkSpeed = 100
        Notify("Speed Hack ATIVADO", Color3.fromRGB(0, 255, 0), "⚡", 2)
        CreateParticleEffect(RootPart.Position, BrickColor.new("Bright green"), 5)
    else
        Humanoid.WalkSpeed = 16
        Notify("Speed Hack DESATIVADO", Color3.fromRGB(255, 0, 0), "🐌", 2)
    end
end

local function ToggleJumpPower()
    HackStates.JumpPower = not HackStates.JumpPower
    if HackStates.JumpPower then
        Humanoid.JumpPower = 120
        Notify("Jump Power ATIVADO", Color3.fromRGB(0, 255, 0), "🦘", 2)
        CreateParticleEffect(RootPart.Position, BrickColor.new("Bright blue"), 5)
    else
        Humanoid.JumpPower = 50
        Notify("Jump Power DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

local FlyConnection
local FlyBodyVelocity
local function ToggleFly()
    HackStates.Fly = not HackStates.Fly
    if HackStates.Fly then
        FlyBodyVelocity = Instance.new("BodyVelocity")
        FlyBodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        FlyBodyVelocity.Velocity = Vector3.new(0, 0, 0)
        FlyBodyVelocity.Parent = RootPart
        
        local bodyAngularVelocity = Instance.new("BodyAngularVelocity")
        bodyAngularVelocity.MaxTorque = Vector3.new(0, math.huge, 0)
        bodyAngularVelocity.AngularVelocity = Vector3.new(0, 0, 0)
        bodyAngularVelocity.Parent = RootPart
        
        FlyConnection = RunService.Heartbeat:Connect(function()
            local camera = Workspace.CurrentCamera
            local moveVector = Humanoid.MoveDirection
            local cameraDirection = camera.CFrame.LookVector
            
            if moveVector.Magnitude > 0 then
                FlyBodyVelocity.Velocity = (camera.CFrame.RightVector * moveVector.X + cameraDirection * moveVector.Z) * 50
            else
                FlyBodyVelocity.Velocity = Vector3.new(0, 0, 0)
            end
        end)
        
        UserInputService.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Space then
                FlyBodyVelocity.Velocity = FlyBodyVelocity.Velocity + Vector3.new(0, 50, 0)
            elseif input.KeyCode == Enum.KeyCode.LeftShift then
                FlyBodyVelocity.Velocity = FlyBodyVelocity.Velocity + Vector3.new(0, -50, 0)
            end
        end)
        
        Notify("Fly Mode ATIVADO - Use WASD, Space e Shift", Color3.fromRGB(0, 255, 0), "🚁", 3)
        CreateParticleEffect(RootPart.Position, BrickColor.new("Bright yellow"), 8)
    else
        if FlyBodyVelocity then FlyBodyVelocity:Destroy() end
        if RootPart:FindFirstChild("BodyAngularVelocity") then
            RootPart.BodyAngularVelocity:Destroy()
        end
        if FlyConnection then FlyConnection:Disconnect() end
        Notify("Fly Mode DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- ═══════════════════════════════════════════════════════════════════════════════════
-- FUNÇÕES DOS HACKS - LOCALPLAYER (MELHORADAS)
-- ═══════════════════════════════════════════════════════════════════════════════════

local function ToggleGodMode()
    HackStates.GodMode = not HackStates.GodMode
    if HackStates.GodMode then
        Humanoid.MaxHealth = math.huge
        Humanoid.Health = math.huge
        Notify("Fake GodMode ATIVADO", Color3.fromRGB(255, 215, 0), "🛡️", 2)
        
        -- Visual effect
        local aura = Instance.new("SelectionBox")
        aura.Name = "GodModeAura"
        aura.Adornee = RootPart
        aura.Color3 = Color3.fromRGB(255, 215, 0)
        aura.Transparency = 0.5
        aura.Parent = RootPart
    else
        Humanoid.MaxHealth = 100
        Humanoid.Health = 100
        if RootPart:FindFirstChild("GodModeAura") then
            RootPart.GodModeAura:Destroy()
        end
        Notify("Fake GodMode DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

local function ToggleInvisible()
    HackStates.Invisible = not HackStates.Invisible
    if HackStates.Invisible then
        for _, part in pairs(Character:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 1
            elseif part:IsA("Accessory") then
                part.Handle.Transparency = 1
            end
        end
        -- Tornar face invisível também
        if Character:FindFirstChild("Head") and Character.Head:FindFirstChild("face") then
            Character.Head.face.Transparency = 1
        end
        Notify("Invisibilidade ATIVADA", Color3.fromRGB(128, 0, 128), "👻", 2)
    else
        for _, part in pairs(Character:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Transparency = 0
            elseif part:IsA("Accessory") then
                part.Handle.Transparency = 0
            end
        end
        if Character:FindFirstChild("Head") and Character.Head:FindFirstChild("face") then
            Character.Head.face.Transparency = 0
        end
        Notify("Invisibilidade DESATIVADA", Color3.fromRGB(255, 0, 0), "👤", 2)
    end
end

local function RemoveDoors()
    local doorsRemoved = 0
    for _, door in pairs(Workspace:GetDescendants()) do
        if door.Name:lower():find("door") and door:IsA("BasePart") then
            CreateParticleEffect(door.Position, BrickColor.new("Bright red"), 3)
            door:Destroy()
            doorsRemoved = doorsRemoved + 1
        end
    end
    Notify("Portas REMOVIDAS: " .. doorsRemoved, Color3.fromRGB(255, 100, 0), "🚪", 3)
end

-- Nova função: Rainbow Mode
local RainbowConnection
local function ToggleRainbowMode()
    HackStates.RainbowMode = not HackStates.RainbowMode
    if HackStates.RainbowMode then
        RainbowConnection = RunService.Heartbeat:Connect(function()
            local hue = tick() % 5 / 5
            local color = Color3.fromHSV(hue, 1, 1)
            
            for _, part in pairs(Character:GetChildren()) do
                if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                    part.Color = color
                end
            end
        end)
        Notify("Rainbow Mode ATIVADO", Color3.fromRGB(255, 0, 255), "🌈", 2)
    else
        if RainbowConnection then RainbowConnection:Disconnect() end
        -- Restaurar cores originais
        for _, part in pairs(Character:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.Color = Color3.fromRGB(245, 205, 175) -- Cor de pele padrão
            end
        end
        Notify("Rainbow Mode DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- ═══════════════════════════════════════════════════════════════════════════════════
-- FUNÇÕES DOS HACKS - TROLL (MELHORADAS E NOVAS)
-- ═══════════════════════════════════════════════════════════════════════════════════

local CarSpawnLoop
local function ToggleInfiniteCars()
    HackStates.InfiniteCars = not HackStates.InfiniteCars
    if HackStates.InfiniteCars then
        CarSpawnLoop = RunService.Heartbeat:Connect(function()
            local car = Instance.new("Part")
            car.Name = "TrollCar"
            car.Size = Vector3.new(8, 4, 16)
            car.Material = Enum.Material.Neon
            car.BrickColor = BrickColor.Random()
            car.Position = RootPart.Position + Vector3.new(math.random(-50, 50), 15, math.random(-50, 50))
            car.Parent = Workspace
            
            -- Adicionar wheels para parecer mais realista
            for i = 1, 4 do
                local wheel = Instance.new("Part")
                wheel.Name = "Wheel"
                wheel.Size = Vector3.new(3, 3, 1)
                wheel.Material = Enum.Material.Rubber
                wheel.BrickColor = BrickColor.new("Really black")
                wheel.Shape = Enum.PartType.Cylinder
                wheel.Parent = car
                
                local weld = Instance.new("WeldConstraint")
                weld.Part0 = car
                weld.Part1 = wheel
                weld.Parent = car
                
                if i <= 2 then
                    wheel.CFrame = car.CFrame * CFrame.new(i == 1 and -3 or 3, -2, 6)
                else
                    wheel.CFrame = car.CFrame * CFrame.new(i == 3 and -3 or 3, -2, -6)
                end
            end
            
            game:GetService("Debris"):AddItem(car, 8)
            wait(0.3)
        end)
        Notify("Spawn Infinito de Carros ATIVADO", Color3.fromRGB(255, 20, 147), "🚗", 2)
    else
        if CarSpawnLoop then CarSpawnLoop:Disconnect() end
        Notify("Spawn Infinito de Carros DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

local function FloatAllPlayers()
    local playersFloated = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= Player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.MaxForce = Vector3.new(4000, 4000, 4000)
            bodyPosition.Position = player.Character.HumanoidRootPart.Position + Vector3.new(0, 20, 0)
            bodyPosition.Parent = player.Character.HumanoidRootPart
            
            CreateParticleEffect(player.Character.HumanoidRootPart.Position, BrickColor.new("Bright blue"), 5)
            
            game:GetService("Debris"):AddItem(bodyPosition, 15)
            playersFloated = playersFloated + 1
        end
    end
    Notify("Jogadores FLUTUANDO: " .. playersFloated, Color3.fromRGB(0, 255, 255), "🎈", 3)
end

local function SpawnFireworks()
    for i = 1, 15 do
        local firework = Instance.new("Part")
        firework.Name = "Firework"
        firework.Size = Vector3.new(2, 2, 2)
        firework.Material = Enum.Material.Neon
        firework.BrickColor = BrickColor.Random()
        firework.Shape = Enum.PartType.Ball
        firework.Position = RootPart.Position + Vector3.new(math.random(-30, 30), 40, math.random(-30, 30))
        firework.Parent = Workspace
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        bodyVelocity.Velocity = Vector3.new(math.random(-25, 25), math.random(15, 35), math.random(-25, 25))
        bodyVelocity.Parent = firework
        
        -- Explosão após 2 segundos
        spawn(function()
            wait(2)
            CreateParticleEffect(firework.Position, firework.BrickColor, 20)
            firework:Destroy()
        end)
        
        game:GetService("Debris"):AddItem(firework, 4)
        wait(0.05)
    end
    Notify("Fogos de Artifício DISPARADOS", Color3.fromRGB(255, 69, 0), "🎆", 3)
end

-- Nova função: Disco Mode
local DiscoConnection
local originalLighting = {}
local function ToggleDisco()
    HackStates.Disco = not HackStates.Disco
    if HackStates.Disco then
        -- Salvar configurações originais
        originalLighting.Brightness = Lighting.Brightness
        originalLighting.Ambient = Lighting.Ambient
        originalLighting.ColorShift_Top = Lighting.ColorShift_Top
        originalLighting.ColorShift_Bottom = Lighting.ColorShift_Bottom
        
        DiscoConnection = RunService.Heartbeat:Connect(function()
            local hue = tick() * 2 % 1
            local color = Color3.fromHSV(hue, 1, 1)
            
            Lighting.Brightness = math.random(1, 3)
            Lighting.Ambient = color
            Lighting.ColorShift_Top = color
            Lighting.ColorShift_Bottom = Color3.fromHSV((hue + 0.5) % 1, 1, 1)
        end)
        
        Notify("Disco Mode ATIVADO", Color3.fromRGB(255, 0, 255), "🕺", 2)
    else
        if DiscoConnection then DiscoConnection:Disconnect() end
        
        -- Restaurar configurações originais
        Lighting.Brightness = originalLighting.Brightness
        Lighting.Ambient = originalLighting.Ambient
        Lighting.ColorShift_Top = originalLighting.ColorShift_Top
        Lighting.ColorShift_Bottom = originalLighting.ColorShift_Bottom
        
        Notify("Disco Mode DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- Nova função: Earthquake
local function CauseEarthquake()
    local intensity = 5
    local duration = 10
    
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            spawn(function()
                local humanoidRootPart = player.Character.HumanoidRootPart
                local originalPos = humanoidRootPart.Position
                
                for i = 1, duration * 10 do
                    humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(
                        math.random(-intensity, intensity) / 10,
                        0,
                        math.random(-intensity, intensity) / 10
                    )
                    wait(0.1)
                end
            end)
        end
    end
    
    -- Efeitos visuais
    for i = 1, 20 do
        CreateParticleEffect(
            RootPart.Position + Vector3.new(math.random(-50, 50), 0, math.random(-50, 50)),
            BrickColor.new("Really red"),
            5
        )
    end
    
    Notify("TERREMOTO CAUSADO!", Color3.fromRGB(139, 69, 19), "🌋", 4)
end

-- Nova função: Spam Parts
local SpamPartsConnection
local function ToggleSpamParts()
    HackStates.SpamParts = not HackStates.SpamParts
    if HackStates.SpamParts then
        SpamPartsConnection = RunService.Heartbeat:Connect(function()
            for i = 1, 3 do
                local part = Instance.new("Part")
                part.Name = "SpamPart"
                part.Size = Vector3.new(math.random(1, 5), math.random(1, 5), math.random(1, 5))
                part.Material = Enum.Material.Neon
                part.BrickColor = BrickColor.Random()
                part.Position = RootPart.Position + Vector3.new(
                    math.random(-100, 100),
                    math.random(10, 50),
                    math.random(-100, 100)
                )
                part.Parent = Workspace
                
                local bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
                bodyVelocity.Velocity = Vector3.new(
                    math.random(-50, 50),
                    math.random(-20, 20),
                    math.random(-50, 50)
                )
                bodyVelocity.Parent = part
                
                game:GetService("Debris"):AddItem(part, 5)
            end
            wait(0.1)
        end)
        
        Notify("Spam de Parts ATIVADO", Color3.fromRGB(255, 165, 0), "📦", 2)
    else
        if SpamPartsConnection then SpamPartsConnection:Disconnect() end
        Notify("Spam de Parts DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- Nova função: Anti-Gravity
local AntiGravityConnection
local function ToggleAntiGravity()
    HackStates.AntiGravity = not HackStates.AntiGravity
    if HackStates.AntiGravity then
        AntiGravityConnection = RunService.Heartbeat:Connect(function()
            for _, part in pairs(Workspace:GetDescendants()) do
                if part:IsA("BasePart") and not part.Anchored and part.Parent ~= Character then
                    local bodyVelocity = part:FindFirstChild("AntiGravity")
                    if not bodyVelocity then
                        bodyVelocity = Instance.new("BodyVelocity")
                        bodyVelocity.Name = "AntiGravity"
                        bodyVelocity.MaxForce = Vector3.new(0, math.huge, 0)
                        bodyVelocity.Velocity = Vector3.new(0, 16.2, 0)
                        bodyVelocity.Parent = part
                    end
                end
            end
        end)
        
        Notify("Anti-Gravidade ATIVADO", Color3.fromRGB(138, 43, 226), "🌌", 2)
    else
        if AntiGravityConnection then AntiGravityConnection:Disconnect() end
        
        -- Remover todos os BodyVelocity de anti-gravidade
        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("BodyVelocity") and part.Name == "AntiGravity" then
                part:Destroy()
            end
        end
        
        Notify("Anti-Gravidade DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- ═══════════════════════════════════════════════════════════════════════════════════
-- FUNÇÕES DOS HACKS - EXTRAS (MELHORADAS E NOVAS)
-- ═══════════════════════════════════════════════════════════════════════════════════

local function TeleportToPlayer(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        -- Efeito de teleporte
        CreateParticleEffect(RootPart.Position, BrickColor.new("Bright blue"), 10)
        RootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(3, 0, 0)
        CreateParticleEffect(RootPart.Position, BrickColor.new("Bright green"), 10)
        Notify("Teleportado para " .. playerName, Color3.fromRGB(0, 191, 255), "📍", 3)
    else
        Notify("Jogador não encontrado!", Color3.fromRGB(255, 0, 0), "❌", 2)
    end
end

local function CloneCharacter()
    local clone = Character:Clone()
    clone.Name = Player.Name .. "_Clone"
    clone.Parent = Workspace
    clone.HumanoidRootPart.CFrame = RootPart.CFrame + Vector3.new(5, 0, 0)
    
    -- Fazer o clone seguir o jogador
    spawn(function()
        while clone.Parent == Workspace and clone:FindFirstChild("HumanoidRootPart") do
            clone.HumanoidRootPart.CFrame = RootPart.CFrame + Vector3.new(5, 0, 0)
            wait(0.1)
        end
    end)
    
    CreateParticleEffect(clone.HumanoidRootPart.Position, BrickColor.new("Bright yellow"), 8)
    Notify("Personagem CLONADO", Color3.fromRGB(138, 43, 226), "👥", 2)
end

local function RemoveClothes()
    local itemsRemoved = 0
    for _, item in pairs(Character:GetChildren()) do
        if item:IsA("Shirt") or item:IsA("Pants") or item:IsA("Accessory") then
            item:Destroy()
            itemsRemoved = itemsRemoved + 1
        end
    end
    Notify("Roupas REMOVIDAS: " .. itemsRemoved, Color3.fromRGB(255, 105, 180), "👕", 2)
end

-- Nova função: Walkthrough Walls
local WalkthroughConnection
local function ToggleWalkthrough()
    HackStates.Walkthrough = not HackStates.Walkthrough
    if HackStates.Walkthrough then
        WalkthroughConnection = RunService.Heartbeat:Connect(function()
            for _, part in pairs(Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end)
        Notify("Atravessar Paredes ATIVADO", Color3.fromRGB(0, 255, 255), "🚶", 2)
    else
        if WalkthroughConnection then WalkthroughConnection:Disconnect() end
        for _, part in pairs(Character:GetChildren()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.CanCollide = true
            end
        end
        Notify("Atravessar Paredes DESATIVADO", Color3.fromRGB(255, 0, 0), "🚫", 2)
    end
end

-- Nova função: Become Giant/Tiny
local function ChangeSize(scale)
    local humanoidDescription = Humanoid:GetAppliedDescription()
    
    for _, part in pairs(Character:GetChildren()) do
        if part:IsA("BasePart") then
            part.Size = part.Size * scale
        elseif part:IsA("Accessory") then
            part.Handle.Size = part.Handle.Size * scale
        end
    end
    
    -- Ajustar propriedades do humanoid
    Humanoid.HipHeight = Humanoid.HipHeight * scale
    Humanoid.WalkSpeed = Humanoid.WalkSpeed * (scale * 0.8)
    
    if scale > 1 then
        Notify("Virou GIGANTE!", Color3.fromRGB(255, 69, 0), "🦣", 3)
    else
        Notify("Virou PEQUENO!", Color3.fromRGB(255, 192, 203), "🐭", 3)
    end
    
    CreateParticleEffect(RootPart.Position, BrickColor.Random(), 15)
end

-- Nova função: Crash Server (Visual Effect Only)
local function FakeCrashServer()
    -- Criar muitos objetos temporários para dar a impressão de lag
    for i = 1, 100 do
        spawn(function()
            local part = Instance.new("Part")
            part.Name = "CrashPart"
            part.Size = Vector3.new(50, 50, 50)
            part.Material = Enum.Material.Neon
            part.BrickColor = BrickColor.Random()
            part.Position = Vector3.new(math.random(-1000, 1000), 100, math.random(-1000, 1000))
            part.Parent = Workspace
            
            for j = 1, 10 do
                local subPart = part:Clone()
                subPart.Size = Vector3.new(10, 10, 10)
                subPart.Position = part.Position + Vector3.new(math.random(-25, 25), math.random(-25, 25), math.random(-25, 25))
                subPart.Parent = Workspace
                game:GetService("Debris"):AddItem(subPart, 0.5)
            end
            
            game:GetService("Debris"):AddItem(part, 1)
        end)
    end
    
    -- Efeitos de tela
    for i = 1, 20 do
        spawn(function()
            local screen = Instance.new("ScreenGui")
            screen.Parent = PlayerGui
            
            local frame = Instance.new("Frame")
            frame.Size = UDim2.new(1, 0, 1, 0)
            frame.BackgroundColor3 = Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))
            frame.BackgroundTransparency = 0.8
            frame.Parent = screen
            
            game:GetService("Debris"):AddItem(screen, 0.1)
        end)
    end
    
    Notify("SIMULANDO CRASH DO SERVIDOR! 💥", Color3.fromRGB(255, 0, 0), "💥", 5)
end

-- ═══════════════════════════════════════════════════════════════════════════════════
-- CRIAÇÃO DA INTERFACE GRÁFICA MELHORADA
-- ═══════════════════════════════════════════════════════════════════════════════════

-- ScreenGui principal
local BaxiHub = Instance.new("ScreenGui")
BaxiHub.Name = "BaxiHub_BRK_Enhanced"
BaxiHub.Parent = PlayerGui
BaxiHub.ResetOnSpawn = false

-- Frame principal (inicialmente oculto)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 650, 0, 450)
MainFrame.Position = UDim2.new(0.5, -325, 0.5, -225)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = BaxiHub

-- Bordas arredondadas
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 20)
MainCorner.Parent = MainFrame

-- Gradient background
local mainGradient = Instance.new("UIGradient")
mainGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(20, 20, 30)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(35, 35, 50))
}
mainGradient.Rotation = 45
mainGradient.Parent = MainFrame

-- Sombra do frame principal
local MainShadow = Instance.new("Frame")
MainShadow.Name = "Shadow"
MainShadow.Size = UDim2.new(1, 10, 1, 10)
MainShadow.Position = UDim2.new(0, -5, 0, 5)
MainShadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MainShadow.BackgroundTransparency = 0.7
MainShadow.ZIndex = MainFrame.ZIndex - 1
MainShadow.Parent = BaxiHub

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 20)
ShadowCorner.Parent = MainShadow

-- Header melhorado
local Header = Instance.new("Frame")
Header.Name = "Header"
Header.Size = UDim2.new(1, 0, 0, 60)
Header.Position = UDim2.new(0, 0, 0, 0)
Header.BackgroundColor3 = Color3.fromRGB(10, 10, 20)
Header.BorderSizePixel = 0
Header.Parent = MainFrame

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 20)
HeaderCorner.Parent = Header

-- Header gradient
local headerGradient = Instance.new("UIGradient")
headerGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 10, 20)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 40))
}
headerGradient.Rotation = 90
headerGradient.Parent = Header

-- Logo/Ícone
local Logo = Instance.new("TextLabel")
Logo.Name = "Logo"
Logo.Size = UDim2.new(0, 50, 0, 50)
Logo.Position = UDim2.new(0, 10, 0, 5)
Logo.BackgroundTransparency = 1
Logo.Text = "🌒"
Logo.TextColor3 = Color3.fromRGB(255, 255, 255)
Logo.TextSize = 24
Logo.TextXAlignment = Enum.TextXAlignment.Center
Logo.Font = Enum.Font.GothamBold
Logo.Parent = Header

-- Título melhorado
local HeaderTitle = Instance.new("TextLabel")
HeaderTitle.Name = "Title"
HeaderTitle.Size = UDim2.new(1, -200, 1, 0)
HeaderTitle.Position = UDim2.new(0, 70, 0, 0)
HeaderTitle.BackgroundTransparency = 1
HeaderTitle.Text = "BaxiHub BRK Enhanced v2.0"
HeaderTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
HeaderTitle.TextSize = 20
HeaderTitle.TextXAlignment = Enum.TextXAlignment.Left
HeaderTitle.Font = Enum.Font.GothamBold
HeaderTitle.Parent = Header

-- Subtitle
local Subtitle = Instance.new("TextLabel")
Subtitle.Name = "Subtitle"
Subtitle.Size = UDim2.new(1, -200, 0, 20)
Subtitle.Position = UDim2.new(0, 70, 0, 30)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "by yuyu e imxddos_"
Subtitle.TextColor3 = Color3.fromRGB(180, 180, 180)
Subtitle.TextSize = 12
Subtitle.TextXAlignment = Enum.TextXAlignment.Left
Subtitle.Font = Enum.Font.Gotham
Subtitle.Parent = Header

-- Minimize button
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Size = UDim2.new(0, 35, 0, 35)
MinimizeButton.Position = UDim2.new(1, -80, 0, 12.5)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 193, 7)
MinimizeButton.Text = "─"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.TextSize = 16
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Parent = Header

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 10)
MinimizeCorner.Parent = MinimizeButton

-- Botão fechar melhorado
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Size = UDim2.new(0, 35, 0, 35)
CloseButton.Position = UDim2.new(1, -40, 0, 12.5)
CloseButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
CloseButton.Text = "✕"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 16
CloseButton.Font = Enum.Font.GothamBold
CloseButton.BorderSizePixel = 0
CloseButton.Parent = Header

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 10)
CloseCorner.Parent = CloseButton

-- Sidebar melhorada
local Sidebar = Instance.new("Frame")
Sidebar.Name = "Sidebar"
Sidebar.Size = UDim2.new(0, 160, 1, -60)
Sidebar.Position = UDim2.new(0, 0, 0, 60)
Sidebar.BackgroundColor3 = Color3.fromRGB(15, 15, 25)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

-- Sidebar gradient
local sidebarGradient = Instance.new("UIGradient")
sidebarGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(15, 15, 25)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(30, 30, 45))
}
sidebarGradient.Rotation = 90
sidebarGradient.Parent = Sidebar

-- Content area melhorada
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "ContentFrame"
ContentFrame.Size = UDim2.new(1, -160, 1, -60)
ContentFrame.Position = UDim2.new(0, 160, 0, 60)
ContentFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
ContentFrame.BorderSizePixel = 0
ContentFrame.Parent = MainFrame

-- Content gradient
local contentGradient = Instance.new("UIGradient")
contentGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 35)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(40, 40, 55))
}
contentGradient.Rotation = 45
contentGradient.Parent = ContentFrame

-- Função para criar botões de aba melhorados
local function CreateTabButton(text, icon, position, parent)
    local button = Instance.new("TextButton")
    button.Name = text .. "Tab"
    button.Size = UDim2.new(1, -10, 0, 45)
    button.Position = UDim2.new(0, 5, 0, position)
    button.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    button.Text = ""
    button.BorderSizePixel = 0
    button.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = button
    
    -- Gradient para botão
    local buttonGradient = Instance.new("UIGradient")
    buttonGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 30, 45)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(45, 45, 65))
    }
    buttonGradient.Rotation = 45
    buttonGradient.Parent = button
    
    -- Ícone
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Size = UDim2.new(0, 30, 1, 0)
    iconLabel.Position = UDim2.new(0, 10, 0, 0)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = icon
    iconLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    iconLabel.TextSize = 18
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.Parent = button
    
    -- Texto
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, -50, 1, 0)
    textLabel.Position = UDim2.new(0, 40, 0, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    textLabel.TextSize = 14
    textLabel.TextXAlignment = Enum.TextXAlignment.Left
    textLabel.Font = Enum.Font.Gotham
    textLabel.Parent = button
    
    -- Efeitos hover
    button.MouseEnter:Connect(function()
        CreateTween(button, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(50, 50, 70)})
        CreateTween(iconLabel, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(255, 255, 255)})
        CreateTween(textLabel, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(255, 255, 255)})
    end)
    
    button.MouseLeave:Connect(function()
        if button.BackgroundColor3 ~= Color3.fromRGB(70, 70, 100) then -- Se não está ativo
            CreateTween(button, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(30, 30, 45)})
            CreateTween(iconLabel, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(200, 200, 200)})
            CreateTween(textLabel, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(200, 200, 200)})
        end
    end)
    
    return button
end

-- Função para criar botões de função melhorados
local function CreateFunctionButton(text, icon, position, parent, callback)
    local button = Instance.new("TextButton")
    button.Name = text .. "Button"
    button.Size = UDim2.new(0, 200, 0, 40)
    button.Position = UDim2.new(0, 20, 0, position)
    button.BackgroundColor3 = Color3.fromRGB(45, 45, 65)
    button.Text = ""
    button.BorderSizePixel = 0
    button.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = button
    
    -- Gradient
    local buttonGradient = Instance.new("UIGradient")
    buttonGradient.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0, Color3.fromRGB(45, 45, 65)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(65, 65, 90))
    }
    buttonGradient.Rotation = 45
    buttonGradient.Parent = button
    
    -- Ícone
    local iconLabel = Instance.new("TextLabel")
    iconLabel.Size = UDim2.new(0, 35, 1, 0)
    iconLabel.Position = UDim2.new(0, 5, 0, 0)
    iconLabel.BackgroundTransparency = 1
    iconLabel.Text = icon
    iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    iconLabel.TextSize = 16
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.Parent = button
    
    -- Texto
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, -40, 1, 0)
    textLabel.Position = UDim2.new(0, 35, 0, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = text
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextSize = 12
    textLabel.TextXAlignment = Enum.TextXAlignment.Left
    textLabel.Font = Enum.Font.Gotham
    textLabel.Parent = button
    
    -- Efeitos hover e clique
    button.MouseEnter:Connect(function()
        CreateTween(button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(70, 70, 95),
            Size = UDim2.new(0, 205, 0, 40)
        })
    end)
    
    button.MouseLeave:Connect(function()
        CreateTween(button, TweenInfo.new(0.2), {
            BackgroundColor3 = Color3.fromRGB(45, 45, 65),
            Size = UDim2.new(0, 200, 0, 40)
        })
    end)
    
    button.MouseButton1Click:Connect(function()
        -- Animação de clique
        CreateTween(button, TweenInfo.new(0.1), {Size = UDim2.new(0, 195, 0, 38)})
        wait(0.1)
        CreateTween(button, TweenInfo.new(0.1), {Size = UDim2.new(0, 200, 0, 40)})
        
        callback()
    end)
    
    return button
end

-- Criando abas melhoradas
local MainTab = CreateTabButton("Main", "⚡", 10, Sidebar)
local LocalPlayerTab = CreateTabButton("LocalPlayer", "👤", 65, Sidebar)
local TrollTab = CreateTabButton("Troll", "😈", 120, Sidebar)
local ExtrasTab = CreateTabButton("Extras", "🎯", 175, Sidebar)
local AdvancedTab = CreateTabButton("Advanced", "🔧", 230, Sidebar)

-- Frames de conteúdo para cada aba
local MainContent = Instance.new("ScrollingFrame")
MainContent.Name = "MainContent"
MainContent.Size = UDim2.new(1, -20, 1, -20)
MainContent.Position = UDim2.new(0, 10, 0, 10)
MainContent.BackgroundTransparency = 1
MainContent.ScrollBarThickness = 8
MainContent.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
MainContent.CanvasSize = UDim2.new(0, 0, 0, 600)
MainContent.Visible = true
MainContent.Parent = ContentFrame

local LocalPlayerContent = Instance.new("ScrollingFrame")
LocalPlayerContent.Name = "LocalPlayerContent"
LocalPlayerContent.Size = UDim2.new(1, -20, 1, -20)
LocalPlayerContent.Position = UDim2.new(0, 10, 0, 10)
LocalPlayerContent.BackgroundTransparency = 1
LocalPlayerContent.ScrollBarThickness = 8
LocalPlayerContent.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
LocalPlayerContent.CanvasSize = UDim2.new(0, 0, 0, 600)
LocalPlayerContent.Visible = false
LocalPlayerContent.Parent = ContentFrame

local TrollContent = Instance.new("ScrollingFrame")
TrollContent.Name = "TrollContent"
TrollContent.Size = UDim2.new(1, -20, 1, -20)
TrollContent.Position = UDim2.new(0, 10, 0, 10)
TrollContent.BackgroundTransparency = 1
TrollContent.ScrollBarThickness = 8
TrollContent.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
TrollContent.CanvasSize = UDim2.new(0, 0, 0, 800)
TrollContent.Visible = false
TrollContent.Parent = ContentFrame

local ExtrasContent = Instance.new("ScrollingFrame")
ExtrasContent.Name = "ExtrasContent"
ExtrasContent.Size = UDim2.new(1, -20, 1, -20)
ExtrasContent.Position = UDim2.new(0, 10, 0, 10)
ExtrasContent.BackgroundTransparency = 1
ExtrasContent.ScrollBarThickness = 8
ExtrasContent.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
ExtrasContent.CanvasSize = UDim2.new(0, 0, 0, 700)
ExtrasContent.Visible = false
ExtrasContent.Parent = ContentFrame

local AdvancedContent = Instance.new("ScrollingFrame")
AdvancedContent.Name = "AdvancedContent"
AdvancedContent.Size = UDim2.new(1, -20, 1, -20)
AdvancedContent.Position = UDim2.new(0, 10, 0, 10)
AdvancedContent.BackgroundTransparency = 1
AdvancedContent.ScrollBarThickness = 8
AdvancedContent.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
AdvancedContent.CanvasSize = UDim2.new(0, 0, 0, 500)
AdvancedContent.Visible = false
AdvancedContent.Parent = ContentFrame

-- Função para alternar entre abas melhorada
local function SwitchTab(tabName)
    MainContent.Visible = (tabName == "Main")
    LocalPlayerContent.Visible = (tabName == "LocalPlayer")
    TrollContent.Visible = (tabName == "Troll")
    ExtrasContent.Visible = (tabName == "Extras")
    AdvancedContent.Visible = (tabName == "Advanced")
    
    -- Atualizar cores dos botões das abas
    for _, button in pairs(Sidebar:GetChildren()) do
        if button:IsA("TextButton") then
            local iconLabel = button:FindFirstChild("TextLabel")
            local textLabel = button:GetChildren()[3] -- O segundo TextLabel
            
            if button.Name == tabName .. "Tab" then
                button.BackgroundColor3 = Color3.fromRGB(70, 70, 100)
                if iconLabel then iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255) end
                if textLabel then textLabel.TextColor3 = Color3.fromRGB(255, 255, 255) end
            else
                button.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
                if iconLabel then iconLabel.TextColor3 = Color3.fromRGB(200, 200, 200) end
                if textLabel then textLabel.TextColor3 = Color3.fromRGB(200, 200, 200) end
            end
        end
    end
end

-- Eventos das abas
MainTab.MouseButton1Click:Connect(function() SwitchTab("Main") end)
LocalPlayerTab.MouseButton1Click:Connect(function() SwitchTab("LocalPlayer") end)
TrollTab.MouseButton1Click:Connect(function() SwitchTab("Troll") end)
ExtrasTab.MouseButton1Click:Connect(function() SwitchTab("Extras") end)
AdvancedTab.MouseButton1Click:Connect(function() SwitchTab("Advanced") end)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÕES DAS FUNÇÕES - MAIN
-- ═══════════════════════════════════════════════════════════════════════════════════

CreateFunctionButton("Speed Hack", "⚡", 20, MainContent, ToggleSpeed)
CreateFunctionButton("Jump Power", "🦘", 70, MainContent, ToggleJumpPower)
CreateFunctionButton("Fly Mode", "🚁", 120, MainContent, ToggleFly)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÕES DAS FUNÇÕES - LOCALPLAYER
-- ═══════════════════════════════════════════════════════════════════════════════════

CreateFunctionButton("Fake GodMode", "🛡️", 20, LocalPlayerContent, ToggleGodMode)
CreateFunctionButton("Invisibilidade", "👻", 70, LocalPlayerContent, ToggleInvisible)
CreateFunctionButton("Remover Portas", "🚪", 120, LocalPlayerContent, RemoveDoors)
CreateFunctionButton("Rainbow Mode", "🌈", 170, LocalPlayerContent, ToggleRainbowMode)
CreateFunctionButton("Atravessar Paredes", "🚶", 220, LocalPlayerContent, ToggleWalkthrough)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÕES DAS FUNÇÕES - TROLL
-- ═══════════════════════════════════════════════════════════════════════════════════

CreateFunctionButton("Spawn Carros Infinitos", "🚗", 20, TrollContent, ToggleInfiniteCars)
CreateFunctionButton("Flutuar Jogadores", "🎈", 70, TrollContent, FloatAllPlayers)
CreateFunctionButton("Fogos de Artifício", "🎆", 120, TrollContent, SpawnFireworks)
CreateFunctionButton("Disco Mode", "🕺", 170, TrollContent, ToggleDisco)
CreateFunctionButton("Terremoto", "🌋", 220, TrollContent, CauseEarthquake)
CreateFunctionButton("Spam Parts", "📦", 270, TrollContent, ToggleSpamParts)
CreateFunctionButton("Anti-Gravidade", "🌌", 320, TrollContent, ToggleAntiGravity)
CreateFunctionButton("Fake Crash Server", "💥", 370, TrollContent, FakeCrashServer)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÕES DAS FUNÇÕES - EXTRAS
-- ═══════════════════════════════════════════════════════════════════════════════════

-- Input para teleporte melhorado
local TpInput = Instance.new("TextBox")
TpInput.Name = "TpInput"
TpInput.Size = UDim2.new(0, 200, 0, 35)
TpInput.Position = UDim2.new(0, 20, 0, 20)
TpInput.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
TpInput.Text = "Nome do Jogador"
TpInput.TextColor3 = Color3.fromRGB(255, 255, 255)
TpInput.TextSize = 12
TpInput.Font = Enum.Font.Gotham
TpInput.BorderSizePixel = 0
TpInput.Parent = ExtrasContent

local TpCorner = Instance.new("UICorner")
TpCorner.CornerRadius = UDim.new(0, 10)
TpCorner.Parent = TpInput

-- Gradient para input
local inputGradient = Instance.new("UIGradient")
inputGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(35, 35, 50)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 50, 70))
}
inputGradient.Rotation = 45
inputGradient.Parent = TpInput

CreateFunctionButton("Teleportar", "📍", 65, ExtrasContent, function()
    TeleportToPlayer(TpInput.Text)
end)

CreateFunctionButton("Clonar Personagem", "👥", 115, ExtrasContent, CloneCharacter)
CreateFunctionButton("Remover Roupas", "👕", 165, ExtrasContent, RemoveClothes)

-- Botões de tamanho
CreateFunctionButton("Virar Gigante", "🦣", 215, ExtrasContent, function()
    ChangeSize(2)
end)

CreateFunctionButton("Virar Pequeno", "🐭", 265, ExtrasContent, function()
    ChangeSize(0.5)
end)

CreateFunctionButton("Tamanho Normal", "👤", 315, ExtrasContent, function()
    ChangeSize(1)
end)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÕES DAS FUNÇÕES - ADVANCED
-- ═══════════════════════════════════════════════════════════════════════════════════

-- Lista de jogadores
local PlayerList = Instance.new("ScrollingFrame")
PlayerList.Name = "PlayerList"
PlayerList.Size = UDim2.new(0, 200, 0, 150)
PlayerList.Position = UDim2.new(0, 20, 0, 20)
PlayerList.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
PlayerList.ScrollBarThickness = 6
PlayerList.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
PlayerList.BorderSizePixel = 0
PlayerList.Parent = AdvancedContent

local PlayerListCorner = Instance.new("UICorner")
PlayerListCorner.CornerRadius = UDim.new(0, 10)
PlayerListCorner.Parent = PlayerList

-- Função para atualizar lista de jogadores
local function UpdatePlayerList()
    -- Limpar lista atual
    for _, child in pairs(PlayerList:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    local yPos = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= Player then
            local playerButton = Instance.new("TextButton")
            playerButton.Name = player.Name
            playerButton.Size = UDim2.new(1, -10, 0, 30)
            playerButton.Position = UDim2.new(0, 5, 0, yPos)
            playerButton.BackgroundColor3 = Color3.fromRGB(45, 45, 65)
            playerButton.Text = player.Name
            playerButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            playerButton.TextSize = 12
            playerButton.Font = Enum.Font.Gotham
            playerButton.BorderSizePixel = 0
            playerButton.Parent = PlayerList
            
            local buttonCorner = Instance.new("UICorner")
            buttonCorner.CornerRadius = UDim.new(0, 8)
            buttonCorner.Parent = playerButton
            
            -- Ao clicar, colocar nome no input de teleporte
            playerButton.MouseButton1Click:Connect(function()
                TpInput.Text = player.Name
            end)
            
            yPos = yPos + 35
        end
    end
    
    PlayerList.CanvasSize = UDim2.new(0, 0, 0, yPos)
end

-- Atualizar lista a cada 5 segundos
spawn(function()
    while true do
        UpdatePlayerList()
        wait(5)
    end
end)

-- Título da lista
local PlayerListTitle = Instance.new("TextLabel")
PlayerListTitle.Name = "PlayerListTitle"
PlayerListTitle.Size = UDim2.new(0, 200, 0, 25)
PlayerListTitle.Position = UDim2.new(0, 20, 0, -5)
PlayerListTitle.BackgroundTransparency = 1
PlayerListTitle.Text = "📋 Lista de Jogadores"
PlayerListTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
PlayerListTitle.TextSize = 14
PlayerListTitle.TextXAlignment = Enum.TextXAlignment.Left
PlayerListTitle.Font = Enum.Font.GothamBold
PlayerListTitle.Parent = AdvancedContent

-- Botões avançados
CreateFunctionButton("Resetar Personagem", "🔄", 200, AdvancedContent, function()
    Character:BreakJoints()
    Notify("Personagem Resetado", Color3.fromRGB(255, 165, 0), "🔄", 2)
end)

CreateFunctionButton("Limpar Workspace", "🧹", 250, AdvancedContent, function()
    local itemsRemoved = 0
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj.Name == "TrollCar" or obj.Name == "SpamPart" or obj.Name == "Firework" or obj.Name == "CrashPart" then
            obj:Destroy()
            itemsRemoved = itemsRemoved + 1
        end
    end
    Notify("Workspace Limpo: " .. itemsRemoved .. " itens", Color3.fromRGB(0, 255, 0), "🧹", 3)
end)

CreateFunctionButton("Desativar Todos Hacks", "🚫", 300, AdvancedContent, function()
    -- Resetar todos os estados
    for hack, state in pairs(HackStates) do
        if state then
            -- Executar função de desativação baseada no hack
            if hack == "Speed" then ToggleSpeed()
            elseif hack == "JumpPower" then ToggleJumpPower()
            elseif hack == "Fly" then ToggleFly()
            elseif hack == "GodMode" then ToggleGodMode()
            elseif hack == "Invisible" then ToggleInvisible()
            elseif hack == "RainbowMode" then ToggleRainbowMode()
            elseif hack == "InfiniteCars" then ToggleInfiniteCars()
            elseif hack == "Disco" then ToggleDisco()
            elseif hack == "SpamParts" then ToggleSpamParts()
            elseif hack == "AntiGravity" then ToggleAntiGravity()
            elseif hack == "Walkthrough" then ToggleWalkthrough()
            end
        end
    end
    
    -- Desconectar todas as conexões ativas
    for _, connection in pairs(ActiveConnections) do
        if connection then
            connection:Disconnect()
        end
    end
    ActiveConnections = {}
    
    Notify("Todos os Hacks DESATIVADOS", Color3.fromRGB(255, 0, 0), "🚫", 3)
end)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- BOTÃO DE TOGGLE PRINCIPAL MELHORADO
-- ═══════════════════════════════════════════════════════════════════════════════════

local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 70, 0, 70)
ToggleButton.Position = UDim2.new(0, 20, 0, 20)
ToggleButton.BackgroundColor3 = Color3.fromRGB(45, 45, 65)
ToggleButton.Text = ""
ToggleButton.BorderSizePixel = 0
ToggleButton.Parent = BaxiHub

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 35)
ToggleCorner.Parent = ToggleButton

-- Gradient para toggle button
local toggleGradient = Instance.new("UIGradient")
toggleGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(45, 45, 65)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(70, 70, 95))
}
toggleGradient.Rotation = 45
toggleGradient.Parent = ToggleButton

-- Ícone no toggle button
local ToggleIcon = Instance.new("TextLabel")
ToggleIcon.Name = "ToggleIcon"
ToggleIcon.Size = UDim2.new(1, 0, 1, 0)
ToggleIcon.BackgroundTransparency = 1
ToggleIcon.Text = "🌒"
ToggleIcon.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleIcon.TextSize = 28
ToggleIcon.Font = Enum.Font.GothamBold
ToggleIcon.Parent = ToggleButton

-- Sombra do toggle button
local ToggleShadow = Instance.new("Frame")
ToggleShadow.Name = "ToggleShadow"
ToggleShadow.Size = UDim2.new(1, 6, 1, 6)
ToggleShadow.Position = UDim2.new(0, -3, 0, 3)
ToggleShadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ToggleShadow.BackgroundTransparency = 0.8
ToggleShadow.ZIndex = ToggleButton.ZIndex - 1
ToggleShadow.Parent = BaxiHub

local ToggleShadowCorner = Instance.new("UICorner")
ToggleShadowCorner.CornerRadius = UDim.new(0, 35)
ToggleShadowCorner.Parent = ToggleShadow

-- Draggable toggle button melhorado
local dragging = false
local dragStart = nil
local startPos = nil
local dragThreshold = 5 -- Pixels para considerar como drag

ToggleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = ToggleButton.Position
        
        -- Animação de press
        CreateTween(ToggleButton, TweenInfo.new(0.1), {Size = UDim2.new(0, 65, 0, 65)})
        CreateTween(ToggleShadow, TweenInfo.new(0.1), {Size = UDim2.new(1, 4, 1, 4), Position = UDim2.new(0, -2, 0, 2)})
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        
        -- Só começar a arrastar se passou do threshold
        if math.abs(delta.X) > dragThreshold or math.abs(delta.Y) > dragThreshold then
            ToggleButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            ToggleShadow.Position = UDim2.new(0, -3, 0, 3)
        end
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        if dragging then
            local delta = input.Position - dragStart
            
            -- Se não foi um drag significativo, tratar como clique
            if math.abs(delta.X) <= dragThreshold and math.abs(delta.Y) <= dragThreshold then
                -- Toggle hub visibility
                local hubOpen = MainFrame.Visible or MainFrame.Size.X.Offset > 0
                
                if not hubOpen then
                    MainFrame.Visible = true
                    MainFrame.Size = UDim2.new(0, 0, 0, 0)
                    MainShadow.Size = UDim2.new(1, 0, 1, 0)
                    
                    CreateTween(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                        Size = UDim2.new(0, 650, 0, 450)
                    })
                    CreateTween(MainShadow, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                        Size = UDim2.new(1, 10, 1, 10)
                    })
                    
                    -- Animar botão
                    CreateTween(ToggleButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(0, 255, 0)})
                    CreateTween(ToggleIcon, TweenInfo.new(0.3), {Rotation = 180})
                else
                    CreateTween(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
                        Size = UDim2.new(0, 0, 0, 0)
                    })
                    CreateTween(MainShadow, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
                        Size = UDim2.new(1, 0, 1, 0)
                    })
                    
                    CreateTween(ToggleButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(45, 45, 65)})
                    CreateTween(ToggleIcon, TweenInfo.new(0.3), {Rotation = 0})
                    
                    spawn(function()
                        wait(0.4)
                        MainFrame.Visible = false
                    end)
                end
            end
            
            -- Animação de release
            CreateTween(ToggleButton, TweenInfo.new(0.2), {Size = UDim2.new(0, 70, 0, 70)})
            CreateTween(ToggleShadow, TweenInfo.new(0.2), {Size = UDim2.new(1, 6, 1, 6), Position = UDim2.new(0, -3, 0, 3)})
        end
        dragging = false
    end
end)

-- Botão minimize
MinimizeButton.MouseButton1Click:Connect(function()
    CreateTween(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0)
    })
    CreateTween(MainShadow, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(1, 0, 1, 0)
    })
    
    CreateTween(ToggleButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(45, 45, 65)})
    CreateTween(ToggleIcon, TweenInfo.new(0.3), {Rotation = 0})
    
    spawn(function()
        wait(0.3)
        MainFrame.Visible = false
    end)
end)

-- Evento de fechar melhorado
CloseButton.MouseButton1Click:Connect(function()
    -- Animação de saída épica
    CreateTween(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(0, 0, 0, 0),
        Rotation = 360
    })
    CreateTween(MainShadow, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
        Size = UDim2.new(1, 0, 1, 0)
    })
    
    CreateTween(ToggleButton, TweenInfo.new(0.3), {BackgroundColor3 = Color3.fromRGB(45, 45, 65)})
    CreateTween(ToggleIcon, TweenInfo.new(0.3), {Rotation = 0})
    
    spawn(function()
        wait(0.5)
        MainFrame.Visible = false
        MainFrame.Rotation = 0 -- Reset rotation
    end)
end)

-- ═══════════════════════════════════════════════════════════════════════════════════
-- INICIALIZAÇÃO E CONFIGURAÇÕES MELHORADAS
-- ═══════════════════════════════════════════════════════════════════════════════════

-- Garantir que o script funcione quando o personagem respawnar
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = Character:WaitForChild("Humanoid")
    RootPart = Character:WaitForChild("HumanoidRootPart")
    
    -- Resetar estados dos hacks
    for hack, _ in pairs(HackStates) do
        HackStates[hack] = false
    end
    
    -- Desconectar conexões ativas
    for _, connection in pairs(ActiveConnections) do
        if connection then
            connection:Disconnect()
        end
    end
    ActiveConnections = {}
    
    Notify("Personagem Respawnou - Hacks Resetados", Color3.fromRGB(255, 255, 0), "🔄", 3)
end)

-- Detecção de movimento do toggle button para prevenir cliques acidentais
local lastTogglePosition = ToggleButton.Position
spawn(function()
    while true do
        if ToggleButton.Position ~= lastTogglePosition then
            lastTogglePosition = ToggleButton.Position
        end
        wait(0.1)
    end
end)

-- Sistema de updates automático da interface
spawn(function()
    while true do
        -- Atualizar cores do toggle button baseado no status dos hacks
        local activeHacks = 0
        for _, state in pairs(HackStates) do
            if state then activeHacks = activeHacks + 1 end
        end
        
        if activeHacks > 0 and not MainFrame.Visible then
            -- Pulsar o botão quando há hacks ativos
            CreateTween(ToggleButton, TweenInfo.new(0.5), {BackgroundColor3 = Color3.fromRGB(255, 215, 0)})
            wait(0.5)
            CreateTween(ToggleButton, TweenInfo.new(0.5), {BackgroundColor3 = Color3.fromRGB(45, 45, 65)})
            wait(0.5)
        else
            wait(2)
        end
    end
end)

-- Efeitos de inicialização
spawn(function()
    -- Animação de entrada do toggle button
    ToggleButton.Size = UDim2.new(0, 0, 0, 0)
    ToggleButton.Position = UDim2.new(0.5, 0, 0.5, 0)
    
    CreateTween(ToggleButton, TweenInfo.new(1, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 70, 0, 70),
        Position = UDim2.new(0, 20, 0, 20)
    })
    
    CreateTween(ToggleShadow, TweenInfo.new(1, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out), {
        Size = UDim2.new(1, 6, 1, 6)
    })
    
    wait(1)
    
    -- Efeito de partículas na inicialização
    CreateParticleEffect(Vector3.new(0, 50, 0), BrickColor.new("Bright blue"), 20)
end)

-- Notificação de carregamento melhorada
spawn(function()
    wait(1.5) -- Esperar animação terminar
    Notify("BaxiHub BRK Enhanced Carregado!", Color3.fromRGB(0, 255, 0), "🚀", 4)
    wait(1)
    Notify("Nova versão com interface melhorada!", Color3.fromRGB(0, 191, 255), "✨", 3)
    wait(1)
    Notify("Novos trolls e funcionalidades adicionadas!", Color3.fromRGB(255, 20, 147), "😈", 3)
end)

print("🌒 BaxiHub BRK Enhanced v2.0 carregado com sucesso!")
print("📊 Interface melhorada com novos efeitos visuais")
print("🎯 Novos trolls e funcionalidades avançadas")
print("🚀 Sistema de notificações aprimorado")

--[[ 
🌒 ENHANCED SPECIAL THANKS 🌒

Aos corajosos que testam o impossível.
Aos que transformam códigos em caos organizado.
Ao Brookhaven, que se tornou nosso playground.
À criatividade sem limites dos trolls.

Criado por: @imxddos e yuyu 
Enhanced Version 2.0
© 2025 - LOLFaction Enhanced

🎮 Novidades v2.0:
- Interface moderna com gradientes e sombras
- Sistema de notificações aprimorado com ícones
- Novos trolls: Disco Mode, Terremoto, Anti-Gravidade
- Lista de jogadores interativa
- Efeitos de partículas e animações suaves
- Botão draggable com detecção inteligente
- Sistema de abas scrollable
- Funcionalidades avançadas de reset e limpeza
- Rainbow Mode e atravessar paredes
- Mudança de tamanho (gigante/pequeno)
- Fake crash server para trollagem épica
]]
