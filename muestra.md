# Kotlin

``` import java.io.File
import kotlin.random.Random
import kotlin.system.exitProcess
const val WHITE = "\u001B[37m"
const val BLACK = "\u001B[30m"
const val GREEN = "\u001B[32m"
const val YELLOW = "\u001B[33m"
const val BG_RED = "\u001B[41m"
const val RESET = "\u001B[0m"

fun random(cifras:Int, numeroInicio:Int, numeroFinal:Int):String{
    // Usamos un Set para asegurarnos de que no haya números repetidos
    val numerosGenerados = mutableSetOf<Int>()

    while (numerosGenerados.size < cifras) {
        // Rango de 0-9 (incluye 0 y excluye el 10)
        val ramdonNumber = Random.nextInt(numeroInicio, numeroFinal)

        // Agregamos el número al Set; si ya existe, no lo añade
        numerosGenerados.add(ramdonNumber)
    }

    // Convertimos el Set a una lista, la unimos y la convertimos a Int
    val numeroAleatorio = numerosGenerados.joinToString("")
    File("./intentos.txt").writeText("Numero secreto: $numeroAleatorio")
    return numeroAleatorio
}

fun juego(numeroAleatorio:String, intentos:Int, cifras:Int, numeroInicio: Int, numeroFinal: Int):String {
    println()
    println("El juego consisten en adivinar un número de $cifras cifras cada cifra puede contener un numero del $numeroInicio al ${numeroFinal-1} y tendras $intentos intentos.")
    println()

    for (i in intentos downTo 1) {
        println("Te quedan $i intentos")
        print("Escribe un número de $cifras cifras sin números repetidos:")
        var numeroEscrito = readln()

        // Validación para asegurarse de que el número tiene la cantidad de cifras correcta y cada cifra está dentro del rango
        while (numeroEscrito.length != cifras || numeroEscrito.any { it.digitToInt() !in numeroInicio until numeroFinal }) {
            print("${BG_RED}${BLACK}ERROR${RESET} ${WHITE}escribiste un número con más o menos de $cifras cifras o con dígitos fuera del rango $numeroInicio - ${numeroFinal-1}. Vuelve a introducir el número: ")
            numeroEscrito = readln()
        }

        val resultado =(resultadoJuego(numeroAleatorio,numeroEscrito, cifras))

        if (resultado.contains(numeroAleatorio)){
            return "$resultado con ${GREEN}${i-1} ${WHITE}intentos sobrantes"
        }
        println(resultado)
        File("./intentos.txt").appendText(System.lineSeparator() +"Intento $i: $numeroEscrito | $resultado")
        println()
    }
    return "Perdiste, el numero aleatorio es $numeroAleatorio"
}

fun resultadoJuego(numeroAleatorio: String, numeroEscrito:String, cifras:Int):String{
    var contadorAciertos = 0
    var contadorContiene = 0

    //forEachIndexed guarda el indice y valor de numero escrito ejemplo: 4321 index de 4 = 0 y valor de 4 = 4
    numeroEscrito.forEachIndexed { index, valor ->
        //Si el valor es igual al valor del numero aleatorio en la pocicion del indice
        if (valor == numeroAleatorio[index]) {
            contadorAciertos++
            //De lo contrario se comprueba si el valor de numeroEscrito esta en numeroAleatorio
        } else if (numeroAleatorio.contains(valor)) {
            contadorContiene++
        }
    }
    return if (contadorAciertos == cifras){
        "Correcto el numero aleatorio es ${GREEN}$numeroAleatorio${WHITE}"
    } else {
        "Correcto: ${GREEN}$contadorAciertos ${WHITE}| Correcto en posición incorrecta: ${YELLOW}$contadorContiene${WHITE}"
    }
}

fun main() {
    val cifras = 4
    val numeroInicio = 0
    val numeroFinal = 10
    val intentos = 4
    val file = File("./intentos.txt")
    if (!file.exists()) { // Crea el archivo si no existe
        file.createNewFile()
    }

    println("${WHITE}1. Jugar")
    println("2. Intento anterior")
    println("3. Salir")
    val selector = readln().toInt()
    when (selector) {
        1 ->{ // Empieza el juego
            val numeroAleatorio = random(cifras, numeroInicio, numeroFinal)
            // println("$numeroAleatorio Borrar en la version final")
            println(juego(numeroAleatorio, intentos, cifras, numeroInicio, numeroFinal))
            println()
            main()
        }
        2 -> { // Lee el fichero
            println()
            val leer = File("./intentos.txt").readText()
            println(leer)
            println()
            main()
        }
        3 ->{ // Finaliza el programa
            println("Gracias por jugar")
            exitProcess(0) // En ese caso no es necesario pero lo dejo para referencias futuras
        }
    }
}
