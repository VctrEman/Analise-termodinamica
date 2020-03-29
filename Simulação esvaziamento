import CoolProp as CP; print(CP.__version__, CP.__gitrevision__)
from CoolProp.CoolProp import PropsSI
import seaborn as sns
sns.set()

def Flow(P1 ,P2 = 1,T = 300,SG = 1.517 ,state = 'gas' ,Cv = 3.5 ):      #input units: pascal, kelvin
  #receber P1,P2,  SG do n2o, gas, cv da valv svh 141 de seção 0.5
    T *=1.8       #kelvin to rankine
    P1 *= 14.5038/10**5
    P2 *= 14.5038/10**5
    PC = 0.53*P1
    P = 14.6959
    Q = -1

    if P2>=PC:
        Q = Cv*1.7*( P*(P1-P2)*520/(SG*T) )**0.5    #m3/h

    elif P2<PC:
        Q = Cv*1.7*(P1*(520/(T*2*SG) ))**0.5

    elif (state != 'gas'):
        Q = 0.2268*Cv*1.7*((P1-P2)/SG)**0.5      #m3/h

    else:
        print("error")

    return (Q/3600)#m3/s


P1 = P0 = 55*10**5
rho = PropsSI('D', 'T', (273+30), 'P', P1, 'SRK::N2O')
Vcil = 2*10**-3
mtk1 = rho*Vcil
print(mtk1)

Pamb = P3 = P2 = 1.0325*10**5

dt = 2**-7  #1/128
Cvt = 1.284549
Cv1 = 3.5
massa = []
pressao1 = []
pressao2 = []
dmdt = []
t = []
c = 0

while True:
    pressao1.append(P1*10**-5)#bar
    massa.append(mtk1)#kg
    t.append(c)

    for k in np.linspace(3,3.5,2):
      Cv1 = k
    
    pressao2.append(P2*10**-5)#bar
    rho = PropsSI('D', 'T', (273 + 30), 'P', P1, 'SRK::N2O')

    mtk2 = mtk1 - rho*Flow(P1,P2)*dt
    mtk1 = mtk2
    dmdt.append(Flow(P1,P2))#kg/s

    rho = PropsSI('D', 'T', (273+30), 'P', P1, 'SRK::N2O')
    
  
    if c < 0*(dt):#so pra n ter que apagar
        #P1 = P0 - (P1 - P3) * (Cvt / Cv1) ** 2
        pass
    else:
        P1 =  PropsSI('P', 'T', (273+30), 'D', mtk1/Vcil, 'SRK::N2O')
    
    P2 = P1 - (P1 - P3) * (Cvt / Cv1) ** 2
    c+=dt

    if rho*Flow(P1,P2) <= 1*10**-3 or P1 <= 9*10**5:
        print('massa final',mtk1,'Pfinal',P1,'duração',t[-1],'SI units')
        break

print(len(t))
import matplotlib.pyplot as plt
plt.plot(t,dmdt,'r--',linewidth = 2,label="massa   (g/s)")
plt.plot(t,pressao1,'r-*',linewidth = 2,label="P1   (Bar)")
plt.plot(t,pressao2,'p--',linewidth = 2,label="P2   (Bar)")
plt.legend(shadow=True, fancybox=True, )
plt.show()
