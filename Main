import pandas as pd

def setConditions():
    state = {}
    print("Set the follow info:")
    T = 300#float(input("Type T (K):\n"))
    P1 = 72 #float(input("Type cilinder pressure (bar):\n"))      #cilinder pressure
    P2 = 1#float(input('P2:\n'))
    state['T'] = T      #em flow deve chegar em rankine
    state['P1'] = P1*10**5      #pascal
    state['P2'] = P2*10**5
    return state            #return state dict, for tk gas state, P1 is considered

def chooseGas():
    df = pd.read_csv(r'C:\Users\victo\PycharmProjects\motor testes\gases', sep=',', )  # index_col= 'GAS'
    cdf = df[['GAS', 'MM', 'TC', 'PC', 'W']]
    cdf = df.set_index('GAS')
    gas = 'N2O'      #str(input('gas')).upper()   remexer aquiiiiiiiiiii
    a = cdf.loc[gas]
    a = (dict(a))
    return a

def calcProp(**gas):  # entra com a tupla gas
    gas.update(setConditions())

    R = 8.31447  # j/molk
    TC = gas['TC']
    PC = gas['PC']
    PC *= 10**5
    w = gas['W']
    MM = gas['MM']    #g/mol
    MM *= 10**-3    #kg/mol
    T = gas['T']
    P = gas['P1']   #pascal
    Rg = R / MM

    k = 0.37464 + 1.54226 * w - 0.26992 * w ** 2
    Tr = T / TC
    alfa = (1 + k * (1 - Tr ** 0.5)) ** 2
    a = 0.457224 * alfa * (Rg * TC) ** 2 / PC
    b = 0.0778 * Rg * TC / PC

    pcoef = {}
    pcoef['a'] = a
    pcoef['b'] = b
    pcoef['MM'] = MM
    pcoef['Rg'] = Rg
    pcoef['T'] = T
    pcoef['P1'] = gas['P1']
    pcoef['P2'] = gas['P2']
    return pcoef

def CalcV(**coef):  # obter vol esp, chute gases ideais pv = nrt, V = R*T/P
    R = 8.31451  # m3*Pa/K*mol
    a = coef['a']
    b = coef['b']
    T = coef['T']
    P = coef['P1']  # pascal
    Rg = coef['Rg']

    cont = 0

  # NEWTON-RAPHSON
    def fv(v1):
        return P - Rg * T / (v1 - b) + a / (v1 ** 2 + 2 * v1 * b - b ** 2)

    def dfv(v1):
        return Rg * T / (v1 - b) ** 2 - 2 * a * (v1 - b) / ((v1 ** 2 + 2 * v1 * b - b ** 2) ** 2)

    c = Rg * T / P  # chute inicial,  dos gases ideais, vou testar 0.5, dps testar a validade, isso pode ser influenciado pela polaridade, melhor ignorar isso***
    """posso tentar diminuir o valor do chute pra melhorar a precisao, o chute nao muda a tecnica, muda a precisao da raiz, passar isso rapido"""
    def Rec(c):
        nonlocal cont
        v2 = 0
        v1 = c
        v2 = v1 - (fv(v1) / dfv(v1))
        cont += 1

        if abs((v2 - v1) * 100 / v1) >= 0.000001:
            round(v1, 17)
            v1 = v2
            return Rec(v2)

        else:
            print("volume molar m3/kg",v1,'apos',cont,'iteraçoes')
            return v1      #volume molar, massa especifica

    return Rec(c)


def P(V,T,Rg,a,b):  # by peng, Vesp e massa sao informaçoes q tem q ser dadas
    P = Rg * T / (V - b) - a / (V ** 2 + 2 * b * V - b ** 2)
    return P

#receber T em rankine, deve receber a pressao em bar, condição de ser gas, abordar cavitação no relatorio, erro do monitoramento em linha***
def Flow(P1 ,P2 = 1,T = 300,SG = 1.517 ,state = 'gas' ,Cv = 3.5 ):      #input units: bar, kelvin
  #receber P1,P2,  SG do n2o, gas, cv da valv svh 141 de seção 0.5
    T *=1.8       #kelvin to rankine
    P1 *= 14.5038
    P2 *= 14.5038
    PC = 0.53*P1
    P = 14.6959
    Q = -1

    if P2>=PC:
        Q = Cv*1.7*( P*(P1-P2)*520/(SG*T) )**0.5    #m3/s

    elif P2<PC:
        Q = Cv*1.7*(P1*(520/(T*2*SG) ))**0.5

    elif (state != 'gas'):
        Q = 0.2268*Cv*1.7*((P1-P2)/SG)**0.5      #m3/h

    else:
        print("error")

    return (Q/3600)#m3/s




#import numpy as np
#ps = np.linspace(10,70,7)   #cuidado pra nao passar da pressao critica
#l = []
#for i in ps:
 #   coef['P1'] = i*10**5
#    l.append(CalcV(**coef))

def getboundary():
    b = {}
    kwargs = chooseGas()
    Vcil = 5 #float(input("Type cilinder vol:(L)\n"))
    Vcil *=10**-3
    coef = calcProp(**kwargs)
    coef['Vcil'] = Vcil
    V = CalcV(**coef)  # especific volume at initial conditions
    coef['V'] = V
    mi = Vcil / V       #initial mass
    print(V,Vcil)
    coef['mi'] = mi

    return coef

def simu():
    coef = getboundary()
    Rg = coef['Rg']
    b = coef['b']
    a = coef['a']
    T = coef['T']

    mi = coef['mi']
    P1 = coef['P1']
    T = coef['T']

    Vcil = coef['Vcil']
    V = coef['V']
    print(1/V)
simu()
    #h = 0.125       #passo
#309.4, 72.54
"""Ma = mi
    for k in range(100):
        dVdt = Flow(P1)
        print(dVdt)
        Mc = Ma - 2*h*dVdt
        Ma = Mc
        V = Mc/Vcil
        P1 = P(V,T,Rg,a,b,)
        """

    #daqui se determina a nova vazao
