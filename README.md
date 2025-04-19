-- Configurações da bomba
local bombSize = 1000
local explosionSize = 20000 -- Raio da explosão massiva
local damage = 99999 -- Dano massivo para eliminar jogadores
local explosionPressure = 500000 -- Pressão da explosão
local destroyRadiusPercent = 100 -- Destruição de partes

-- Cria a Part da bomba
local bombPart = Instance.new("Part")
bombPart.Name = "BombaGlobalUniversal"
bombPart.Size = Vector3.new(bombSize, bombSize, bombSize)
bombPart.Anchored = false -- Para cair
bombPart.CanCollide = true
bombPart.Position = Vector3.new(0, 5000 + bombSize/2, 0) -- Spawn alto
bombPart.Shape = Enum.PartType.Ball
bombPart.Color = Color3.fromRGB(255, 0, 0)
bombPart.Material = Enum.Material.Neon

-- Cria um script dentro da bomba
local explodeScript = Instance.new("Script")
explodeScript.Parent = bombPart
explodeScript.Name = "ExplodirAoTocarQualquerChao"

-- Função para a explosão global
local function detonateGlobal()
    local explosion = Instance.new("Explosion")
    explosion.Position = bombPart.Position
    explosion.BlastRadius = explosionSize
    explosion.BlastPressure = explosionPressure
    explosion.DestroyJointRadiusPercent = destroyRadiusPercent
    explosion.Parent = workspace

    -- Dano a todos os jogadores
    for i, player in pairs(game.Players:GetPlayers()) do
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") then
            local humanoid = character:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health > 0 then
                humanoid.Health -= damage
            end
        end
    end

    -- Limpeza
    bombPart:Destroy()
    explodeScript:Destroy()
end

-- Detecta a colisão com o chão usando Raycast
bombPart:GetPropertyChangedSignal("Position"):Connect(function()
    -- Pequena espera para garantir que a posição foi atualizada após a física
    wait(0.1)

    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {bombPart} -- Ignora a própria bomba
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

    -- Raycast ligeiramente abaixo do centro da bomba
    local raycastResult = workspace:Raycast(bombPart.Position, Vector3.new(0, -bombPart.Size.Y/2 - 0.5, 0), raycastParams)

    if raycastResult then
        -- Se o raycast atingir algo, consideramos que tocou o chão
        detonateGlobal()
    end
end)

-- Adiciona a bomba ao Workspace
bombPart.Parent = workspace
