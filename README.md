#!/bin/bash

# Función para imprimir el título
print_title() {
echo -e "\e[1;34m
   ___ __  _                _
    |  |_ | | |\/| | |\ |  |_| |
    |  |_ | \ |  | | | \|  | | |_
\e[0m"
}

print_help() {
    echo "Uso: $0 [opción]"
    echo "Opciones:"
    echo "  1. -p, --pid         Imprimir el PID del launcher."
    echo "  2. -t, --launch      Lanzar el terminal."
    echo "  3. -k, --kill        Cerrar el programa."
    echo "  4. -h, --help        Mostrar este mensaje de ayuda."
    echo "  5. -g, --guess       Juego de adivinanzas."
    echo "  6. -f, --phrases     Generador de frases aleatorias."
    echo "  7. -a, --ascii       Generador de ASCII Art."
    echo "  8. -q, --quit        Salir del programa."
}

print_launcher_pid() {
    echo "PID del launcher: $$"
}

launch_terminal() {
    gnome-terminal &
    sleep 1
    child_pid=$(pstree -p $$ | grep -o '([0-9]\+)' | grep -o '[0-9]\+')
    echo "PID del hijo (terminal): $child_pid"
}

kill_program() {
    child_pid=$(pstree -p $$ | grep -o '([0-9]\+)' | grep -o '[0-9]\+')
    parent_pid=$(ps -o ppid= -p $child_pid)
    echo "Matando proceso hijo (terminal) con PID: $child_pid"
    kill -9 $child_pid
    echo "Matando proceso padre (launcher) con PID: $parent_pid"
    kill -9 $parent_pid
}

# Comando: Juego de adivinanzas
guess_number() {
    local number=$((RANDOM % 100 + 1))
    local guess
    while true; do
        read -p "Intenta adivinar un número entre 1 y 100: " guess
        if [[ $guess -eq $number ]]; then
            echo "¡Adivinaste! El número era $number."
            break
        elif [[ $guess -lt $number ]]; then
            echo "Intenta con un número mayor."
        else
            echo "Intenta con un número menor."
        fi
    done
}

# Comando: Generador de frases aleatorias
generate_phrase() {
    local phrases=("La vida es como una bicicleta, para mantener el equilibrio debes seguir adelante. - Albert Einstein"
                    "No importa lo lento que vayas mientras no pares. - Confucio"
                    "Hazlo o no lo hagas, pero no lo intentes. - Yoda")
    local random_index=$((RANDOM % ${#phrases[@]}))
    echo "${phrases[random_index]}"
}

# Comando: Generador de ASCII Art
print_ascii_art() {
    echo "    ___________"
    echo "   /           \\"
    echo "  /             \\"
    echo " /_______________\\"
    echo "|_________________|"
    echo "|   __       __   |"
    echo "|  |__|     |__|  |"
    echo "|_________________|"
}

# Imprimir el título al inicio
print_title

# Bucle para el prompt
while true; do
    read -p "Ingrese el número de la opción que desea (-h para ayuda): " option
    case $option in
        1 | -p | --pid)
            print_launcher_pid
            ;;
        2 | -t | --launch)
            launch_terminal
            ;;
        3 | -k | --kill)
            kill_program
            ;;
        4 | -h | --help)
            print_help
            ;;
        5 | -g | --guess)
            guess_number
            ;;
        6 | -f | --phrases)
            generate_phrase
            ;;
        7 | -a | --ascii)
            print_ascii_art
            ;;
        8 | -q | --quit)
            exit
            ;;
        *)
            echo "Opción inválida: $option"
            ;;
    esac
done
