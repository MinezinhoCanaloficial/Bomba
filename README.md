-- Configurações da bomba
local bombSize = 1000
local explosionSize = 5000 -- Ajuste o tamanho da explosão
local damage = 100 -- Ajuste o dano da explosão
local explosionPressure = 200000 -- Ajuste a força da explosão

-- Cria a Part da bomba
local bombPart = Instance.new("Part")
bombPart.Name = "BombaInstantanea"
bombPart.Size = Vector3.new(bombSize, bombSize, bombSize)
bombPart.Anchored = false -- Para que possa cair e tocar em coisas
bombPart.CanCollide = true
bombPart.Position = Vector3.new(0, 5000 + bombSize/2, 0) -- Spawn um pouco acima para cair
bombPart.Shape = Enum.PartType.Ball -- Formato de bola para melhor detecção de colisão
bombPart.Color = Color3.fromRGB(255, 0, 0) -- Cor vermelha para indicar perigo
bombPart.Material = Enum.Material.Neon -- Material brilhante

-- Cria um script dentro da bomba
local explodeScript = Instance.new("Script")
explodeScript.Parent = bombPart
explodeScript.Name = "ExplodirAoTocar"

-- Função para criar a explosão
local function detonate()
    local explosion = Instance.new("Explosion")
    explosion.Position = bombPart.Position
    explosion.BlastRadius = explosionSize
    explosion.BlastPressure = explosionPressure
    explosion.DestroyJointRadiusPercent = 0 -- Evita destruir juntas
    explosion.Parent = workspace

    -- Lida com o dano aos Humanoids
    explosion.Hit:Connect(function(hitPart)
        local character = hitPart.Parent
        local humanoid = character:FindFirstChild("Humanoid")
        if humanoid and humanoid.Health > 0 then
            humanoid.Health -= damage
        end
    end)

    -- Destrói a bomba após a explosão
    bombPart:Destroy()
    explodeScript:Destroy() -- Limpa o script também
end

-- Conecta o evento Touched para detectar colisões
bombPart.Touched:Connect(function(otherPart)
    if otherPart and otherPart.Parent ~= bombPart then -- Garante que não está colidindo consigo mesma
        detonate()
    end
end)

-- Adiciona a bomba ao Workspace
bombPart.Parent = workspace
