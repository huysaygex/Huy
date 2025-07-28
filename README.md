local npc = script.Parent
local humanoid = npc:WaitForChild("Humanoid")
local root = npc:WaitForChild("HumanoidRootPart")

local PathfindingService = game:GetService("PathfindingService")
local Players = game:GetService("Players")

local ATTACK_DISTANCE = 5
local ATTACK_COOLDOWN = 1.5
local lastAttack = 0

function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (player.Character.HumanoidRootPart.Position - root.Position).Magnitude
            if dist < shortestDistance then
                shortestDistance = dist
                closestPlayer = player
            end
        end
    end

    return closestPlayer
end

function moveToTarget(target)
    local path = PathfindingService:CreatePath({
        AgentRadius = 2,
        AgentHeight = 5,
        AgentCanJump = true,
        AgentJumpHeight = 10,
        AgentCanClimb = true
    })

    path:ComputeAsync(root.Position, target.Position)

    if path.Status == Enum.PathStatus.Complete then
        for _, waypoint in pairs(path:GetWaypoints()) do
            humanoid:MoveTo(waypoint.Position)
            humanoid.MoveToFinished:Wait()

            local distance = (target.Position - root.Position).Magnitude
            if distance < ATTACK_DISTANCE then
                return true -- Sẵn sàng tấn công
            end
        end
    end
    return false
end

function attack(target)
    if tick() - lastAttack >= ATTACK_COOLDOWN then
        lastAttack = tick()
        local humanoidTarget = target.Parent:FindFirstChild("Humanoid")
        if humanoidTarget then
            humanoidTarget:TakeDamage(10)
        end
    end
end

while true do
    local player = getClosestPlayer()
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local targetPos = player.Character.HumanoidRootPart
        local inRange = moveToTarget(targetPos)
        if inRange then
            attack(targetPos)
        end
    end
    wait(0.5)
end
