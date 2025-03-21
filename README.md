-- Mini City RP - Script Base
-- Criado para gerenciar economia, empregos e veículos

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local MarketplaceService = game:GetService("MarketplaceService")

-- Criando tabela de jogadores e economia
local playerData = {}

-- Configuração inicial dos jogadores
local function onPlayerJoin(player)
    playerData[player.UserId] = {
        Money = 100, -- Dinheiro inicial
        Job = "Nenhum",
        Vehicles = {}
    }
    
    -- Enviar dados para o jogador
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player
    
    local money = Instance.new("IntValue")
    money.Name = "Money"
    money.Value = playerData[player.UserId].Money
    money.Parent = leaderstats
end

-- Atualizar dinheiro do jogador
local function updateMoney(player, amount)
    if playerData[player.UserId] then
        playerData[player.UserId].Money = playerData[player.UserId].Money + amount
        player.leaderstats.Money.Value = playerData[player.UserId].Money
    end
end

-- Sistema de emprego
local jobs = {
    ["Entregador"] = 50,
    ["Taxista"] = 70,
    ["Policial"] = 100
}

local function setJob(player, jobName)
    if jobs[jobName] then
        playerData[player.UserId].Job = jobName
        print(player.Name .. " agora é " .. jobName)
    end
end

-- Sistema de pagamento por tempo
local function paySalary()
    while true do
        wait(60) -- Pagamento a cada 60 segundos
        for _, player in pairs(Players:GetPlayers()) do
            local job = playerData[player.UserId].Job
            if jobs[job] then
                updateMoney(player, jobs[job])
                print(player.Name .. " recebeu " .. jobs[job] .. " pelo trabalho de " .. job)
            end
        end
    end
end

-- Comprar veículo
local vehicles = {
    ["Carro"] = 500,
    ["Moto"] = 300
}

local function buyVehicle(player, vehicleName)
    if vehicles[vehicleName] and playerData[player.UserId].Money >= vehicles[vehicleName] then
        playerData[player.UserId].Money = playerData[player.UserId].Money - vehicles[vehicleName]
        table.insert(playerData[player.UserId].Vehicles, vehicleName)
        player.leaderstats.Money.Value = playerData[player.UserId].Money
        print(player.Name .. " comprou um " .. vehicleName)
    else
        print("Dinheiro insuficiente!")
    end
end

-- Eventos
Players.PlayerAdded:Connect(onPlayerJoin)
task.spawn(paySalary)
