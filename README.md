-- Configurações da bomba
local bombSize = 1000
local explosionSize = 20000 -- Aumentei bastante o raio da explosão
local damage = 99999 -- Dano massivo para eliminar jogadores
local explosionPressure = 500000 -- Mantendo uma pressão alta
local destroyRadiusPercent = 100 -- Para garantir que partes sejam destruídas

-- Cria a Part da bomba
local bombPart = Instance.new("Part")
bombPart.Name = "BombaGlobal"
bombPart.Size = Vector3.new(bombSize, bombSize, bombSize)
bombPart.Anchored = false -- Para que possa cair
bombPart.CanCollide = true
bombPart.Position = Vector3.new(0, 5000 + bombSize/2, 0) -- Spawn um pouco acima
bombPart.Shape = Enum.PartType.Ball
bombPart.Color = Color3.fromRGB(255, 0, 0)
bombPart.Material = Enum.Material.Neon

-- Cria um script dentro da bomba
local explodeScript = Instance.new("Script")
explodeScript.Parent = bombPart
explodeScript.Name = "ExplodirAoTocarChao"

-- Função para criar a explosão global
local function detonateGlobal()
    local explosion = Instance.new("Explosion")
    explosion.Position = bombPart.Position
    explosion.BlastRadius = explosionSize
    explosion.BlastPressure = explosionPressure
    explosion.DestroyJointRadiusPercent = destroyRadiusPercent
    explosion.Parent = workspace

    -- Itera por todos os personagens jogadores no servidor e aplica dano
    for i, player in pairs(game.Players:GetPlayers()) do
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") then
            local humanoid = character:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health > 0 then
                humanoid.Health -= damage
            end
        end
    end

    -- Destrói a bomba e o script
    bombPart:Destroy()
    explodeScript:Destroy()
end

-- Conecta o evento Touched para detectar a colisão com o chão
bombPart.Touched:Connect(function(otherPart)
    -- Verifica se a parte tocada é considerada o chão (BasePlate ou Floor - você pode adicionar mais)
    if otherPart.Name == "BasePlate" or otherPart.Name == "Floor" then
        detonateGlobal()
    end
end)

-- Adiciona a bomba ao Workspace
bombPart.Parent = workspace
