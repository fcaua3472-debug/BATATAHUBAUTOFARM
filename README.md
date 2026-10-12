-- BATATA HUB AUTO FARM
local P=game:GetService("Players")
local R=game:GetService("RunService")
local U=game:GetService("UserInputService")
local plr=P.LocalPlayer
local cam=workspace.CurrentCamera
local HUB="🥔 BATATA HUB AUTO FARM"
local DC="https://discord.gg/BQMcA9DPhN"

-- ═══ KEYS ═══
local KEYS={
    ["OWNERSKEYINFINITY"]={t="i"},
    ["TESTERSKEYFREE"]={t="l",u=2},
    ["tornadohuntersfreekey-133"]={t="d"},
}
local uk,dk={},{}

local function validar(k)
    if not k or k=="" then return false,"Digite uma key!" end
    local D=KEYS[k]
    if not D then return false,"❌ Key inválida!" end
    if D.t=="i" then return true,"✅ Owner!" end
    if D.t=="l" then
        local u=uk[k] or 0
        if u>=D.u then return false,"❌ Expirada" end
        uk[k]=u+1
        return true,"✅ Restam "..(D.u-uk[k])
    end
    if D.t=="d" then
        local a=dk[k]
        if a then
            local p=tick()-a
            if p>=86400 then dk[k]=tick() return true,"✅ Renovada!" end
            local r=86400-p
            return false,"❌ "..math.floor(r/3600).."h "..math.floor((r%3600)/60).."min"
        end
        dk[k]=tick()
        return true,"✅ 1 dia!"
    end
    return false,"❌ Erro"
end

-- ═══ TEXTOS ═══
local TXT={
    pt={seg="SEGUIR Ball mesh",par="PARAR",cli="Clicar TODOS os tools",anti="Anti-Void",fly="Fly Manual",nc="Noclip",unl="UNLOAD",prn="Pronto",sel="Selecione o Idioma",getk="Pegar Key",ins="Insira a Key",ver="VERIFICAR",cop="✅ Copiado!"},
    en={seg="FOLLOW Ball mesh",par="STOP",cli="Click ALL tools",anti="Anti-Void",fly="Manual Fly",nc="Noclip",unl="UNLOAD",prn="Ready",sel="Select Language",getk="Get Key",ins="Insert Key",ver="VERIFY",cop="✅ Copied!"}
}
local T=TXT.pt

-- ═══ CONFIG ═══
local CFG={A="Ball mesh",D=8,DC=15,V=220,DEL=0.05,VY=-50}
local seg,voa,anti,nc,unl=false,false,true,false,false
local cF,cA,cN,bV,bG=nil,nil,nil,nil,nil
local vel=CFG.V
local dir=Vector3.zero
local tk={}
local alvo,ultS,jaC=nil,nil,false
local tc,ub={},0
local G={}

-- ═══ HELPERS ═══
local function posOf(o)
    if not o then return nil end
    if o:IsA("BasePart") then return o.Position end
    if o:IsA("Model") then
        local p=o.PrimaryPart or o:FindFirstChildWhichIsA("BasePart")
        return p and p.Position
    end
end

local function achar()
    local b=workspace:FindFirstChild(CFG.A,true)
    if b and (b:IsA("BasePart") or b:IsA("Model")) then return b end
end

local function ncOn()
    if nc and cN then return end
    nc=true
    cN=R.Stepped:Connect(function()
        if not nc or unl then return end
        local ch=plr.Character
        if ch then
            for _,p in ipairs(ch:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide=false end
            end
        end
    end)
end

local function ncOff()
    nc=false
    if cN then cN:Disconnect() cN=nil end
end

local function togNC()
    if nc then ncOff() else ncOn() end
    if G.bNC then G.bNC.Text="👻 "..T.nc..": "..(nc and "ON" or "OFF") end
end

local function antiOn()
    if anti and cA then return end
    anti=true
    cA=R.Heartbeat:Connect(function()
        if not anti or unl then return end
        local ch=plr.Character
        local hrp=ch and ch:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local pos=hrp.Position
        if pos.Y>CFG.VY+20 then ultS=pos end
        if pos.Y<CFG.VY then
            local d=ultS or Vector3.new(0,50,0)
            if alvo and alvo.Parent then
                local p=posOf(alvo)
                if p then d=p+Vector3.new(0,20,0) end
            end
            hrp.CFrame=CFrame.new(d)
            hrp.AssemblyLinearVelocity=Vector3.zero
        end
    end)
end

local function pegarT()
    local a=tick()
    if a-ub<2 and #tc>0 then
        local v={}
        for _,t in ipairs(tc) do if t and t.Parent then table.insert(v,t) end end
        if #v>0 then return v end
    end
    ub=a
    tc={}
    local bp=plr:FindFirstChildOfClass("Backpack")
    if bp then for _,i in ipairs(bp:GetChildren()) do if i:IsA("Tool") then table.insert(tc,i) end end end
    local ch=plr.Character
    if ch then for _,i in ipairs(ch:GetChildren()) do if i:IsA("Tool") then table.insert(tc,i) end end end
    return tc
end

local function clickar(t)
    if not t or not t.Parent then return end
    local cd=t:FindFirstChildOfClass("ClickDetector")
    if cd then pcall(function() if fireclickdetector then fireclickdetector(cd) end end) end
    local pp=t:FindFirstChildOfClass("ProximityPrompt")
    if pp then pcall(function() pp:InputHoldBegin() task.wait(0.05) pp:InputHoldEnd() end) end
    pcall(function()
        local ch=plr.Character
        local h=ch and ch:FindFirstChildOfClass("Humanoid")
        if h and t:IsA("Tool") then
            local o=t.Parent
            t.Parent=ch
            task.wait(0.03)
            t:Activate()
            task.wait(0.03)
            if o then t.Parent=o end
        end
    end)
end

local function clickTodos()
    local t=pegarT()
    if #t==0 then return 0 end
    local hs={}
    for _,x in ipairs(t) do
        if x and x.Parent then
            local h=Instance.new("Highlight")
            h.FillColor=Color3.fromRGB(0,255,150)
            h.OutlineColor=Color3.fromRGB(255,255,255)
            h.FillTransparency=0.4
            h.DepthMode=Enum.HighlightDepthMode.AlwaysOnTop
            h.Parent=x
            table.insert(hs,h)
        end
    end
    for _,x in ipairs(t) do if x and x.Parent then clickar(x) task.wait(CFG.DEL) end end
    for _,h in ipairs(hs) do if h then h:Destroy() end end
    return #t
end

local function flyOn()
    local ch=plr.Character
    if not ch then return end
    local hrp=ch:FindFirstChild("HumanoidRootPart")
    local h=ch:FindFirstChildOfClass("Humanoid")
    if not hrp or not h then return end
    voa=true
    h.PlatformStand=true
    pcall(function()
        for _,e in ipairs(Enum.HumanoidStateType:GetEnumItems()) do
            pcall(function() h:SetStateEnabled(e,false) end)
        end
    end)
    if bV then bV:Destroy() end
    if bG then bG:Destroy() end
    bV=Instance.new("BodyVelocity")
    bV.MaxForce=Vector3.new(1e5,1e5,1e5)
    bV.Velocity=Vector3.zero
    bV.Parent=hrp
    bG=Instance.new("BodyGyro")
    bG.MaxTorque=Vector3.new(1e5,1e5,1e5)
    bG.P=10000 bG.D=500
    bG.CFrame=hrp.CFrame
    bG.Parent=hrp
    cF=R.RenderStepped:Connect(function()
        if not voa or unl then return end
        local c=plr.Character
        if not c then return end
        local r=c:FindFirstChild("HumanoidRootPart")
        if not r or not bV or not bG then return end
        local d=Vector3.zero
        if seg then
            if alvo and alvo.Parent then
                local ap=posOf(alvo)
                if ap then
                    local mp=r.Position
                    local ds=(ap-mp).Magnitude
                    if ds>CFG.D then
                        local f=math.clamp(ds/60,0.6,2.0)
                        d=(ap-mp).Unit*vel*f
                    elseif ds<CFG.D*0.5 then
                        d=(mp-ap).Unit*vel*0.3
                    end
                    if ds<=CFG.DC and not jaC then
                        jaC=true
                        task.spawn(function()
                            if G.st then G.st.Text="👆 ..." end
                            local q=clickTodos()
                            if G.st then G.st.Text="✅ "..q.." tools" end
                        end)
                    end
                    if G.st and not jaC then G.st.Text="🎯 "..math.floor(ds).." studs" end
                end
            end
        else
            local cf=cam.CFrame
            local m=Vector3.zero
            if tk.W then m+=cf.LookVector end
            if tk.S then m-=cf.LookVector end
            if tk.A then m-=cf.RightVector end
            if tk.D then m+=cf.RightVector end
            if tk.Sp then m+=Vector3.new(0,1,0) end
            if tk.Ct then m-=Vector3.new(0,1,0) end
            if m.Magnitude>0 then m=m.Unit end
            dir=dir:Lerp(m*vel,0.2)
            d=dir
        end
        bV.Velocity=d
        if d.Magnitude>0.1 then bG.CFrame=CFrame.new(r.Position,r.Position+d.Unit) end
    end)
end

local function flyOff()
    voa,seg=false,false
    if cF then cF:Disconnect() cF=nil end
    if bV then bV:Destroy() bV=nil end
    if bG then bG:Destroy() bG=nil end
    dir=Vector3.zero
    local ch=plr.Character
    if ch then
        local h=ch:FindFirstChildOfClass("Humanoid")
        if h then
            h.PlatformStand=false
            pcall(function()
                for _,e in ipairs(Enum.HumanoidStateType:GetEnumItems()) do
                    pcall(function() h:SetStateEnabled(e,true) end)
                end
            end)
        end
    end
end

local function seguir()
    if seg then
        seg=false
        if G.bS then G.bS.Text="🎯 "..T.seg end
        return
    end
    alvo=achar()
    seg=true
    jaC=false
    if G.bS then G.bS.Text="🛑 "..T.par end
    if not voa then flyOn() end
end

local function unload()
    unl=true
    voa,seg,anti,nc=false,false,false,false
    if cF then cF:Disconnect() end
    if cA then cA:Disconnect() end
    if cN then cN:Disconnect() end
    if bV then bV:Destroy() end
    if bG then bG:Destroy() end
    for _,g in ipairs(plr.PlayerGui:GetChildren()) do
        if g.Name=="MainGUI" or g.Name=="LangGUI" or g.Name=="KeyGUI" then g:Destroy() end
    end
end

task.spawn(function()
    while not unl do
        task.wait(1)
        if seg and (not alvo or not alvo.Parent) then
            local n=achar()
            if n and n~=alvo then alvo=n jaC=false end
        end
    end
end)

local function rgb()
    local h=0
    local S,Tt={},{}
    local function aS(s) table.insert(S,s) end
    local function aT(t) table.insert(Tt,t) end
    task.spawn(function()
        while not unl do
            task.wait(0.03)
            h=(h+0.008)%1
            for i,s in ipairs(S) do if s and s.Parent then s.Color=Color3.fromHSV((h+i*0.05)%1,0.9,1) end end
            for i,t in ipairs(Tt) do if t and t.Parent then t.TextColor3=Color3.fromHSV((h+i*0.07)%1,0.7,1) end end
        end
    end)
    return aS,aT
end

-- ═══ GUI PRINCIPAL ═══
local function main()
    local sg=Instance.new("ScreenGui")
    sg.Name="MainGUI"
    sg.ResetOnSpawn=false
    sg.IgnoreGuiInset=true
    sg.DisplayOrder=999
    sg.Parent=plr:WaitForChild("PlayerGui")
    local aS,aT=rgb()
    
    local bt=Instance.new("TextButton")
    bt.Size=UDim2.new(0,60,0,60)
    bt.Position=UDim2.new(0,20,0.5,-30)
    bt.BackgroundColor3=Color3.fromRGB(20,20,28)
    bt.Text="🥔"
    bt.TextSize=28
    bt.Font=Enum.Font.GothamBold
    bt.AutoButtonColor=false
    bt.Visible=false
    bt.ZIndex=10
    bt.Parent=sg
    Instance.new("UICorner",bt).CornerRadius=UDim.new(0,16)
    local sB=Instance.new("UIStroke",bt)
    sB.Thickness=2
    aS(sB)
    
    local fr=Instance.new("Frame")
    fr.Size=UDim2.new(0,300,0,455)
    fr.Position=UDim2.new(0,90,0.5,-227)
    fr.BackgroundColor3=Color3.fromRGB(15,15,22)
    fr.BorderSizePixel=0
    fr.ZIndex=5
    fr.Parent=sg
    Instance.new("UICorner",fr).CornerRadius=UDim.new(0,18)
    local sF=Instance.new("UIStroke",fr)
    sF.Thickness=2
    aS(sF)
    
    local tp=Instance.new("Frame")
    tp.Size=UDim2.new(1,0,0,90)
    tp.BackgroundColor3=Color3.fromRGB(20,20,30)
    tp.BorderSizePixel=0
    tp.ZIndex=6
    tp.Parent=fr
    Instance.new("UICorner",tp).CornerRadius=UDim.new(0,18)
    
    local av=Instance.new("ImageLabel")
    av.Size=UDim2.new(0,60,0,60)
    av.Position=UDim2.new(0,15,0,15)
    av.BackgroundColor3=Color3.fromRGB(30,30,40)
    av.BorderSizePixel=0
    av.Image="rbxthumb://type=AvatarHeadShot&id="..plr.UserId.."&w=150&h=150"
    av.ZIndex=7
    av.Parent=tp
    Instance.new("UICorner",av).CornerRadius=UDim.new(0,30)
    local sA=Instance.new("UIStroke",av)
    sA.Thickness=2
    aS(sA)
    
    local n1=Instance.new("TextLabel")
    n1.Size=UDim2.new(1,-90,0,25)
    n1.Position=UDim2.new(0,85,0,18)
    n1.BackgroundTransparency=1
    n1.Text=plr.DisplayName
    n1.TextColor3=Color3.fromRGB(255,255,255)
    n1.TextSize=16
    n1.Font=Enum.Font.GothamBold
    n1.TextXAlignment=Enum.TextXAlignment.Left
    n1.ZIndex=7
    n1.Parent=tp
    
    local n2=Instance.new("TextLabel")
    n2.Size=UDim2.new(1,-90,0,18)
    n2.Position=UDim2.new(0,85,0,42)
    n2.BackgroundTransparency=1
    n2.Text="@"..plr.Name
    n2.TextColor3=Color3.fromRGB(150,150,150)
    n2.TextSize=12
    n2.Font=Enum.Font.Gotham
    n2.TextXAlignment=Enum.TextXAlignment.Left
    n2.ZIndex=7
    n2.Parent=tp
    
    local ti=Instance.new("TextLabel")
    ti.Size=UDim2.new(1,-90,0,20)
    ti.Position=UDim2.new(0,85,0,62)
    ti.BackgroundTransparency=1
    ti.Text=HUB
    ti.TextColor3=Color3.fromRGB(0,255,150)
    ti.TextSize=11
    ti.Font=Enum.Font.GothamBold
    ti.TextXAlignment=Enum.TextXAlignment.Left
    ti.ZIndex=7
    ti.Parent=tp
    aT(ti)
    
    local bx=Instance.new("TextButton")
    bx.Size=UDim2.new(0,28,0,28)
    bx.Position=UDim2.new(1,-34,0,8)
    bx.BackgroundColor3=Color3.fromRGB(255,60,60)
    bx.Text="✕"
    bx.TextColor3=Color3.fromRGB(255,255,255)
    bx.TextSize=14
    bx.Font=Enum.Font.GothamBold
    bx.ZIndex=8
    bx.Parent=tp
    Instance.new("UICorner",bx).CornerRadius=UDim.new(0,8)
    
    local function mk(txt,y,h,cb)
        local b=Instance.new("TextButton")
        b.Size=UDim2.new(1,-24,0,h or 40)
        b.Position=UDim2.new(0,12,0,y)
        b.BackgroundColor3=Color3.fromRGB(25,25,35)
        b.Text=txt
        b.TextColor3=Color3.fromRGB(255,255,255)
        b.TextSize=12
        b.Font=Enum.Font.GothamSemibold
        b.AutoButtonColor=false
        b.ZIndex=6
        b.Parent=fr
        Instance.new("UICorner",b).CornerRadius=UDim.new(0,10)
        local s=Instance.new("UIStroke",b)
        s.Thickness=1.5
        s.Transparency=0.2
        aS(s)
        aT(b)
        b.MouseButton1Click:Connect(function() if cb and not unl then cb() end end)
        return b
    end
    
    G.bS=mk("🎯 "..T.seg,100,46,seguir)
    mk("👆 "..T.cli,152,40,function()
        task.spawn(function()
            if G.st then G.st.Text="👆 ..." end
            local q=clickTodos()
            if G.st then G.st.Text="✅ "..q.." tools" end
        end)
    end)
    G.bNC=mk("👻 "..T.nc..": OFF",198,40,togNC)
    G.bA=mk("🛡️ "..T.anti..": ON",244,40,function()
        if anti then
            anti=false
            if cA then cA:Disconnect() cA=nil end
        else antiOn() end
        G.bA.Text="🛡️ "..T.anti..": "..(anti and "ON" or "OFF")
    end)
    mk("🚀 "..T.fly,290,40,function()
        if voa and not seg then flyOff()
        else seg=false if not voa then flyOn() end end
    end)
    mk("🗑️ "..T.unl,336,44,unload)
    
    G.st=Instance.new("TextLabel")
    G.st.Size=UDim2.new(1,-24,0,30)
    G.st.Position=UDim2.new(0,12,1,-42)
    G.st.BackgroundColor3=Color3.fromRGB(25,25,35)
    G.st.Text="🟢 "..T.prn
    G.st.TextSize=11
    G.st.Font=Enum.Font.GothamSemibold
    G.st.ZIndex=6
    G.st.Parent=fr
    Instance.new("UICorner",G.st).CornerRadius=UDim.new(0,8)
    local sSt=Instance.new("UIStroke",G.st)
    sSt.Thickness=1.5
    aS(sSt)
    aT(G.st)
    
    local mn=false
    local function m1() mn=true fr.Visible=false bt.Visible=true end
    local function m2() mn=false fr.Visible=true bt.Visible=false end
    bx.MouseButton1Click:Connect(m1)
    bt.MouseButton1Click:Connect(m2)
    
    local dr,ds,sp
    tp.InputBegan:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
            dr=true ds=i.Position sp=fr.Position
        end
    end)
    U.InputChanged:Connect(function(i)
        if dr and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then
            local d=i.Position-ds
            fr.Position=UDim2.new(sp.X.Scale,sp.X.Offset+d.X,sp.Y.Scale,sp.Y.Offset+d.Y)
        end
    end)
    U.InputEnded:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then dr=false end
    end)
    
    U.InputBegan:Connect(function(i,g)
        if g or unl then return end
        if i.KeyCode==Enum.KeyCode.RightShift then
            if mn then m2() else m1() end
        elseif i.KeyCode==Enum.KeyCode.F then seguir()
        elseif i.KeyCode==Enum.KeyCode.G then task.spawn(clickTodos)
        elseif i.KeyCode==Enum.KeyCode.X then togNC()
        elseif i.KeyCode==Enum.KeyCode.V then
            if anti then anti=false if cA then cA:Disconnect() cA=nil end else antiOn() end
            if G.bA then G.bA.Text="🛡️ "..T.anti..": "..(anti and "ON" or "OFF") end
        elseif i.KeyCode==Enum.KeyCode.W then tk.W=true
        elseif i.KeyCode==Enum.KeyCode.A then tk.A=true
        elseif i.KeyCode==Enum.KeyCode.S then tk.S=true
        elseif i.KeyCode==Enum.KeyCode.D then tk.D=true
        elseif i.KeyCode==Enum.KeyCode.Space then tk.Sp=true
        elseif i.KeyCode==Enum.KeyCode.LeftControl then tk.Ct=true
        end
    end)
    
    U.InputEnded:Connect(function(i)
        if i.KeyCode==Enum.KeyCode.W then tk.W=false
        elseif i.KeyCode==Enum.KeyCode.A then tk.A=false
        elseif i.KeyCode==Enum.KeyCode.S then tk.S=false
        elseif i.KeyCode==Enum.KeyCode.D then tk.D=false
        elseif i.KeyCode==Enum.KeyCode.Space then tk.Sp=false
        elseif i.KeyCode==Enum.KeyCode.LeftControl then tk.Ct=false
        end
    end)
end

-- ═══ TELA DE KEY ═══
local function key()
    local sg=Instance.new("ScreenGui")
    sg.Name="KeyGUI"
    sg.ResetOnSpawn=false
    sg.IgnoreGuiInset=true
    sg.DisplayOrder=2000
    sg.Parent=plr:WaitForChild("PlayerGui")
    local aS,aT=rgb()
    
    local bg=Instance.new("Frame")
    bg.Size=UDim2.new(1,0,1,0)
    bg.BackgroundColor3=Color3.fromRGB(0,0,0)
    bg.BackgroundTransparency=0.5
    bg.BorderSizePixel=0
    bg.Parent=sg
    
    local p=Instance.new("Frame")
    p.Size=UDim2.new(0,380,0,400)
    p.Position=UDim2.new(0.5,-190,0.5,-200)
    p.BackgroundColor3=Color3.fromRGB(15,15,22)
    p.BorderSizePixel=0
    p.Parent=sg
    Instance.new("UICorner",p).CornerRadius=UDim.new(0,20)
    local sP=Instance.new("UIStroke",p)
    sP.Thickness=2
    aS(sP)
    
    local tp=Instance.new("Frame")
    tp.Size=UDim2.new(1,0,0,85)
    tp.BackgroundColor3=Color3.fromRGB(20,20,30)
    tp.BorderSizePixel=0
    tp.Parent=p
    Instance.new("UICorner",tp).CornerRadius=UDim.new(0,20)
    
    local av=Instance.new("ImageLabel")
    av.Size=UDim2.new(0,55,0,55)
    av.Position=UDim2.new(0,15,0,15)
    av.BackgroundColor3=Color3.fromRGB(30,30,40)
    av.BorderSizePixel=0
    av.Image="rbxthumb://type=AvatarHeadShot&id="..plr.UserId.."&w=150&h=150"
    av.Parent=tp
    Instance.new("UICorner",av).CornerRadius=UDim.new(0,28)
    local sA=Instance.new("UIStroke",av)
    sA.Thickness=2
    aS(sA)
    
    local nl=Instance.new("TextLabel")
    nl.Size=UDim2.new(1,-85,0,25)
    nl.Position=UDim2.new(0,80,0,15)
    nl.BackgroundTransparency=1
    nl.Text=plr.DisplayName
    nl.TextColor3=Color3.fromRGB(255,255,255)
    nl.TextSize=15
    nl.Font=Enum.Font.GothamBold
    nl.TextXAlignment=Enum.TextXAlignment.Left
    nl.Parent=tp
    
    local sb=Instance.new("TextLabel")
    sb.Size=UDim2.new(1,-85,0,25)
    sb.Position=UDim2.new(0,80,0,42)
    sb.BackgroundTransparency=1
    sb.Text="🔑 "..T.ins
    sb.TextColor3=Color3.fromRGB(0,255,150)
    sb.TextSize=13
    sb.Font=Enum.Font.GothamSemibold
    sb.TextXAlignment=Enum.TextXAlignment.Left
    sb.Parent=tp
    aT(sb)
    
    local st=Instance.new("TextLabel")
    st.Size=UDim2.new(1,-40,0,30)
    st.Position=UDim2.new(0,20,0,95)
    st.BackgroundTransparency=1
    st.Text=""
    st.TextColor3=Color3.fromRGB(255,255,255)
    st.TextSize=13
    st.Font=Enum.Font.GothamSemibold
    st.Parent=p
    
    local box=Instance.new("Frame")
    box.Size=UDim2.new(1,-40,0,50)
    box.Position=UDim2.new(0,20,0,135)
    box.BackgroundColor3=Color3.fromRGB(25,25,35)
    box.BorderSizePixel=0
    box.Parent=p
    Instance.new("UICorner",box).CornerRadius=UDim.new(0,10)
    local sB=Instance.new("UIStroke",box)
    sB.Thickness=1.5
    sB.Transparency=0.3
    aS(sB)
    
    local inp=Instance.new("TextBox")
    inp.Size=UDim2.new(1,-20,1,0)
    inp.Position=UDim2.new(0,10,0,0)
    inp.BackgroundTransparency=1
    inp.PlaceholderText="🗝️ XXX-XXXX-XXXX"
    inp.PlaceholderColor3=Color3.fromRGB(120,120,120)
    inp.Text=""
    inp.TextColor3=Color3.fromRGB(255,255,255)
    inp.TextSize=16
    inp.Font=Enum.Font.GothamBold
    inp.TextXAlignment=Enum.TextXAlignment.Center
    inp.ClearTextOnFocus=false
    inp.Parent=box
    
    local bV=Instance.new("TextButton")
    bV.Size=UDim2.new(1,-40,0,50)
    bV.Position=UDim2.new(0,20,0,200)
    bV.BackgroundColor3=Color3.fromRGB(0,200,120)
    bV.Text="✅ "..T.ver
    bV.TextColor3=Color3.fromRGB(255,255,255)
    bV.TextSize=16
    bV.Font=Enum.Font.GothamBold
    bV.AutoButtonColor=false
    bV.Parent=p
    Instance.new("UICorner",bV).CornerRadius=UDim.new(0,12)
    
    local bG=Instance.new("TextButton")
    bG.Size=UDim2.new(1,-40,0,50)
    bG.Position=UDim2.new(0,20,0,265)
    bG.BackgroundColor3=Color3.fromRGB(88,101,242)
    bG.Text="🎫 "..T.getk
    bG.TextColor3=Color3.fromRGB(255,255,255)
    bG.TextSize=16
    bG.Font=Enum.Font.GothamBold
    bG.AutoButtonColor=false
    bG.Parent=p
    Instance.new("UICorner",bG).CornerRadius=UDim.new(0,12)
    
    bV.MouseButton1Click:Connect(function()
        local ok,msg=validar(inp.Text)
        st.Text=msg
        if ok then
            st.TextColor3=Color3.fromRGB(0,255,150)
            task.wait(0.8)
            sg:Destroy()
            main()
            antiOn()
        else
            st.TextColor3=Color3.fromRGB(255,80,80)
        end
    end)
    
    bG.MouseButton1Click:Connect(function()
        pcall(function()
            if setclipboard then
                setclipboard(DC)
                st.Text=T.cop
                st.TextColor3=Color3.fromRGB(0,255,150)
                task.wait(1.5)
                st.Text=""
            end
        end)
    end)
end

-- ═══ SELETOR DE IDIOMA ═══
local function seletor()
    local sg=Instance.new("ScreenGui")
    sg.Name="LangGUI"
    sg.ResetOnSpawn=false
    sg.IgnoreGuiInset=true
    sg.DisplayOrder=2000
    sg.Parent=plr:WaitForChild("PlayerGui")
    local aS,aT=rgb()
    
    local bg=Instance.new("Frame")
    bg.Size=UDim2.new(1,0,1,0)
    bg.BackgroundColor3=Color3.fromRGB(0,0,0)
    bg.BackgroundTransparency=0.5
    bg.BorderSizePixel=0
    bg.Parent=sg
    
    local p=Instance.new("Frame")
    p.Size=UDim2.new(0,360,0,300)
    p.Position=UDim2.new(0.5,-180,0.5,-150)
    p.BackgroundColor3=Color3.fromRGB(15,15,22)
    p.BorderSizePixel=0
    p.Parent=sg
    Instance.new("UICorner",p).CornerRadius=UDim.new(0,20)
    local sP=Instance.new("UIStroke",p)
    sP.Thickness=2
    aS(sP)
    
    local tp=Instance.new("Frame")
    tp.Size=UDim2.new(1,0,0,85)
    tp.BackgroundColor3=Color3.fromRGB(20,20,30)
    tp.BorderSizePixel=0
    tp.Parent=p
    Instance.new("UICorner",tp).CornerRadius=UDim.new(0,20)
    
    local av=Instance.new("ImageLabel")
    av.Size=UDim2.new(0,55,0,55)
    av.Position=UDim2.new(0,15,0,15)
    av.BackgroundColor3=Color3.fromRGB(30,30,40)
    av.BorderSizePixel=0
    av.Image="rbxthumb://type=AvatarHeadShot&id="..plr.UserId.."&w=150&h=150"
    av.Parent=tp
    Instance.new("UICorner",av).CornerRadius=UDim.new(0,28)
    local sA=Instance.new("UIStroke",av)
    sA.Thickness=2
    aS(sA)
    
    local nl=Instance.new("TextLabel")
    nl.Size=UDim2.new(1,-85,0,25)
    nl.Position=UDim2.new(0,80,0,15)
    nl.BackgroundTransparency=1
    nl.Text=plr.DisplayName
    nl.TextColor3=Color3.fromRGB(255,255,255)
    nl.TextSize=15
    nl.Font=Enum.Font.GothamBold
    nl.TextXAlignment=Enum.TextXAlignment.Left
    nl.Parent=tp
    
    local sb=Instance.new("TextLabel")
    sb.Size=UDim2.new(1,-85,0,25)
    sb.Position=UDim2.new(0,80,0,42)
    sb.BackgroundTransparency=1
    sb.Text="🌎 "..T.sel
    sb.TextColor3=Color3.fromRGB(0,255,150)
    sb.TextSize=13
    sb.Font=Enum.Font.GothamSemibold
    sb.TextXAlignment=Enum.TextXAlignment.Left
    sb.Parent=tp
    aT(sb)
    
    local b1=Instance.new("TextButton")
    b1.Size=UDim2.new(1,-30,0,60)
    b1.Position=UDim2.new(0,15,0,100)
    b1.BackgroundColor3=Color3.fromRGB(25,25,35)
    b1.Text="  🇧🇷 Português"
    b1.TextColor3=Color3.fromRGB(255,255,255)
    b1.TextSize=16
    b1.Font=Enum.Font.GothamBold
    b1.TextXAlignment=Enum.TextXAlignment.Left
    b1.AutoButtonColor=false
    b1.Parent=p
    Instance.new("UICorner",b1).CornerRadius=UDim.new(0,10)
    local s1=Instance.new("UIStroke",b1)
    s1.Thickness=1.5
    s1.Transparency=0.3
    aS(s1)
    
    local b2=Instance.new("TextButton")
    b2.Size=UDim2.new(1,-30,0,60)
    b2.Position=UDim2.new(0,15,0,168)
    b2.BackgroundColor3=Color3.fromRGB(25,25,35)
    b2.Text="  🇺🇸 English"
    b2.TextColor3=Color3.fromRGB(255,255,255)
    b2.TextSize=16
    b2.Font=Enum.Font.GothamBold
    b2.TextXAlignment=Enum.TextXAlignment.Left
    b2.AutoButtonColor=false
    b2.Parent=p
    Instance.new("UICorner",b2).CornerRadius=UDim.new(0,10)
    local s2=Instance.new("UIStroke",b2)
    s2.Thickness=1.5
    s2.Transparency=0.3
    aS(s2)
    
    b1.MouseButton1Click:Connect(function()
        T=TXT.pt
        sg:Destroy()
        key()
    end)
    b2.MouseButton1Click:Connect(function()
        T=TXT.en
        sg:Destroy()
        key()
    end)
end

-- ═══ INICIAR ═══
seletor()
