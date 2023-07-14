# examendepartamentos
import datetime

departamentos = []
ganancias_ventas = {
    'A': 0,
    'B': 0,
    'C': 0,
    'D': 0
}

compradores = []

def iniciar():
    for piso in range(1, 11):
        pisos = []
        for i in range(4):
            departamento = {
                "DEP": chr(65 + i) + str(piso),
                'EST': '',
                'PRECIOV': 0 
            }
            if i == 0:  
                departamento['PRECIOV'] = 3800
            elif i == 1: 
                departamento['PRECIOV'] = 3000
            elif i == 2:  
                departamento['PRECIOV'] = 2800
            elif i == 3:  
                departamento['PRECIOV'] = 3500
            pisos.append(departamento)
        departamentos.append(pisos)


def imprimir_departamentos():
    for piso in departamentos:
        for departamento in piso:
            estado_departamento = departamento['EST']
            if estado_departamento == '':
                print(departamento['DEP'], estado_departamento, "\033[1;32m"+"VENTA:", departamento['PRECIOV'], "UF"+ "\033[0m", end=" ")
            elif estado_departamento == 'X':
                print( "\033[1;31m" + departamento['DEP'], estado_departamento + "\033[0m","\033[1;33m"+ "vendido:", departamento['PRECIOV'], "UF" + "\033[0m", end=" ")
        print()


def validar_rut(rut):
    rut = rut.replace("-", "").replace(".", "")
    if len(rut) != 9:
        return False
    if not rut[:-1].isdigit():
        return False

    digito_verificador = rut[-1].upper()
    rut_numeros = list(map(int, rut[:-1]))

    multiplicador = 2
    suma = 0
    for i in range(len(rut_numeros)-1, -1, -1):
        suma += rut_numeros[i] * multiplicador
        multiplicador = (multiplicador + 1) % 8 or 2

    digito_calculado = 11 - (suma % 11)
    digito_calculado = str(digito_calculado) if digito_calculado != 10 else "K"

    return digito_calculado == digito_verificador


def mostrar_lista_compradores():
    print("\033[1;31m"+"Lista de compradores:" + "\033[0m")
    compradores_ordenados = sorted(compradores, key=lambda c: c['RUT'])
    for comprador in compradores_ordenados:
        print("Departamento:", comprador['DEP'])
        print("Nombre:", comprador['NOMBRE'])
        print("RUT:", comprador['RUT'])
        print("--------------------")


def estado():
    nombre_persona = input("Ingrese el nombre de la persona que realiza la compra o arriendo: ")
    if len(nombre_persona) < 4:
        print("El nombre debe tener mínimo 4 caracteres")
        return
    while True:
        rut = input("Ingrese el RUT de la persona que realiza la compra o arriendo: ")
        rut = rut.replace("-", "").replace(".", "")
        if validar_rut(rut):
            print("RUT válido")
            global ventas_totales

            imprimir_departamentos()
            while True:
                nomd = input("Ingrese el departamento a comprar: ")
                nombre_departamento = nomd.upper()
                encontrado = False
                for piso in departamentos:
                    for departamento in piso:
                        if departamento['DEP'] == nombre_departamento:
                            encontrado = True
                            if departamento['EST'] == 'X':
                                print("El departamento No está disponible. Intente nuevamente.")
                                estado()
                            else:
                                while True:
                                    compra = input("Presione [X] para confirmar la Venta: ")
                                    compra = compra.upper()
                                    if compra == "X":
                                        nuevo_estado = compra
                                        departamento['EST'] = nuevo_estado
                                        print("El departamento", nombre_departamento, "Vendido:", "\033[1;31m" + departamento['EST'] + "\033[0m","la operación se ha realizado correctamente")
                                        ganancias_ventas[departamento['DEP'][0]] += departamento['PRECIOV']

                                        compradores.append({
                                            'DEP': nombre_departamento,
                                            'NOMBRE': nombre_persona,
                                            'RUT': rut
                                        })
                                        break
                                    else:
                                        print("Ingrese solo X")
                            break
                if encontrado:
                    break
                else:
                    print("El departamento", nombre_departamento, "no se encontró. Intente nuevamente.")
            break
        else:
            print("RUT no válido")


def mostrar_ventas_por_tipo():
    total_cantidad_vendida = 0
    total_ganancia = 0
    print("\033[1;31m"+"Ventas por tipo de departamento:" + "\033[0m")
    print("  Tipo   Cantidad   Total    ")
    for tipo, ganancia in ganancias_ventas.items():
        cantidad_vendida = round(ganancia / departamentos[0][0]['PRECIOV'])
        print("  ", tipo, "       ", cantidad_vendida, "     ", ganancia, "UF")
        total_cantidad_vendida += cantidad_vendida
        total_ganancia += ganancia
    print("Total:", total_cantidad_vendida, "Ganancia total:", total_ganancia, "UF")


def salir():
    hora_actual = datetime.datetime.now().strftime("%H:%M:%S")
    print("Saliendo del sistema, desarrollado por Alejandro Rodriguez,", hora_actual)


def main():
    iniciar()
    while True:
        print("\033[1;31m"+"                     --- Menú Casa Feliz ---" + "\033[0m")
        print("1. Comprar departamento ")
        print("2. Mostrar departamentos disponibles")
        print("3. Ver listado de compradores")
        print("4. Mostrar ventas de departamento")
        print("5. Salir")

        opcion = input("Ingrese el número de la opción deseada: ")

        if opcion == "1":
            estado()
        elif opcion == "2":
            imprimir_departamentos()
        elif opcion == "3":
            mostrar_lista_compradores()
        elif opcion == "4":
            mostrar_ventas_por_tipo()
        elif opcion == "5":
            salir()
            break
        else:
            print("Opción inválida. Por favor, ingrese un número válido.")


main()
