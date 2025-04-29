
local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/GreenDeno/Venyx-UI-Library/main/source.lua"))()
local venyx = library.new("Vexide,by durst", 5013109572)


local player = game:GetService("Players").LocalPlayer
local rpgSpamEnabled = false
local spamDelay = 0.05 -- Задержка между выстрелами (по умолчанию 0.05 сек)


local function getTargets()
    local targets = {}
    for _, v in ipairs(game:GetService("Players"):GetPlayers()) do
        if v ~= player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            table.insert(targets, v.Character.HumanoidRootPart)
        end
    end
    return targets
end

local function fireInstantRocket()
    local character = player.Character
    if not character or not character:FindFirstChild("RPG") then return end
    local rpg = character.RPG
    
    local targets = getTargets()
    if #targets == 0 then return end 
    
    for _, target in pairs(targets) do
        local fireRocketVector = Vector3.new(0, -1, 0) 
        local fireRocketPosition = target.Position 
        
        game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(fireRocketVector, rpg, rpg, fireRocketPosition)
        
        local fireRocketClientTable = {
            ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
            ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 99999, ["FireRate"] = 99999, ["VehicleDamage"] = 99999, ["ExpName"] = "RPG",
            ["RocketAmount"] = 999, ["ExpRadius"] = 50, ["BoatDamage"] = 99999, ["TankDamage"] = 99999, ["Acceleration"] = 999, ["ShieldDamage"] = 999999,
            ["Distance"] = 999999, ["PlaneDamage"] = 99999, ["GunshipDamage"] = 99999, ["velocity"] = 0, ["ExplosionDamage"] = 99999
        }
        
        local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets:FindFirstChild("RPG Rocket")
        game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
            fireRocketPosition, fireRocketVector, fireRocketClientTable, fireRocketClientInstance1, rpg, rpg, player, nil, { [1] = target }
        )
    end
end


spawn(function()
    while true do
        if rpgSpamEnabled then
            fireInstantRocket()
        end
        wait(spamDelay)
    end
end)

-- Создаем GUI
local page = venyx:addPage("RPG Spammer", 5012544693)
local combatSection = page:addSection("Управление спамом")


combatSection:addToggle("Авто RPG Spam", false, function(state)
    rpgSpamEnabled = state
    venyx:Notify("RPG Spam", state and "Автоспам активирован!" or "Автоспам выключен")
end)


combatSection:addButton("Ручной выстрел", function()
    fireInstantRocket()
    venyx:Notify("RPG", "Произведен ручной выстрел!")
end)


local settingsSection = page:addSection("Настройки")
settingsSection:addSlider("Скорость спама", 0.05, 0.01, 1, function(value)
    spamDelay = value
    venyx:Notify("Настройки", "Скорость спама установлена: " .. value .. " сек")
end)


venyx:SelectPage(venyx.pages[1], true)
