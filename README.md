-- TCS Ultimate Auto-Football
-- Correção Final para Xeno/Delta

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

print("[TCS] Script Atualizado! Rodando...")

-- Espera o player carregar
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")
local RootPart = Character:WaitForChild("HumanoidRootPart")

-- Função robusta para achar a bola
local function getBall()
    -- Tenta nomes comuns
    local names = {"Ball", "SoccerBall", "Soccer_Ball", "Football", "Bola"}
    for _, name in pairs(names) do
        local obj = Workspace:FindFirstChild(name)
        if obj and obj:IsA("BasePart") then return obj end
    end
    -- Fallback: procura por qualquer objeto pequeno e sólido
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj:IsA("BasePart") and obj.Name ~= "HumanoidRootPart" then
            if obj.Size.X < 2 and obj.Transparency == 0 and obj.Anchored == false then
                return obj
            end
        end
    end
    return nil
end

-- Loop Principal
RunService.RenderStepped:Connect(function()
    if not Character or not Character.Parent then return end
    
    local ball = getBall()
    
    if ball then
        -- 1. MOVIMENTO: Correre em direção à bola
        local direction = (ball.Position - RootPart.Position).Unit
        direction = Vector3.new(direction.X, 0, direction.Z) -- Mantém no plano horizontal
        
        if direction.Magnitude > 0.5 then -- Só corre se estiver longe
            Humanoid:Move(direction)
            
            -- 2. AÇÃO: Simula input de chute/controle
            -- Tenta clicar no meio da tela (onde o controle geralmente fica)
            UserInputService:MouseButton1Down()
            task.wait(0.1)
            UserInputService:MouseButton1Up()
            
            -- Tenta apertar Espaço (chute forte)
            UserInputService:SimulateKeyPress(Enum.KeyCode.Space)
            task.wait(0.1)
            UserInputService:SimulateKeyRelease(Enum.KeyCode.Space)
            
            -- Tenta apertar W (andar para frente) explicitamente
            UserInputService:SimulateKeyPress(Enum.KeyCode.W)
            task.wait(0.1)
            UserInputService:SimulateKeyRelease(Enum.KeyCode.W)
            
        else
            -- Se estiver muito perto, para para não atravessar
            Humanoid:Move(Vector3.new(0,0,0))
        end
    else
        -- Se não achar bola, gira lentamente para procurar
        Humanoid:Rotate(0, 1, 0)
    end
end)

print("[TCS] Pronto. O boneco vai correr atrás da bola.")
