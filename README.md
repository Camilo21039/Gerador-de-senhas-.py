#!/usr/bin/env python3
# gerador_infinito.py
import secrets
import time
import argparse
import sys
import signal

CHARS = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"

def gen_password(length):
    return ''.join(secrets.choice(CHARS) for _ in range(length))

def signal_handler(sig, frame):
    print("\nInterrompido pelo usuário. Saindo.")
    sys.exit(0)

def main():
    parser = argparse.ArgumentParser(description="Gerador infinito de senhas (A-Z 0-9). Ctrl+C para parar.")
    parser.add_argument("--length", "-l", type=int, default=8, help="tamanho de cada senha (default 12)") #no "deafault" voce muda o número de digitos que a senha tenha
    parser.add_argument("--rate", "-r", type=float, default=5.0, help="senhas por segundo (default 5.0)")
    parser.add_argument("--file", "-f", type=str, default=None, help="caminho para arquivo (append). Ex: senhas.txt")
    args = parser.parse_args()

    length = max(1, min(1024, args.length))
    rate = max(0.001, args.rate)
    delay = 1.0 / rate
    outfile = None
    if args.file:
        outfile = open(args.file, "a", encoding="utf-8")

    signal.signal(signal.SIGINT, signal_handler)
    signal.signal(signal.SIGTERM, signal_handler)

    try:
        while True:
            pw = gen_password(length)
            # imprime e força flush para ver em tempo real
            print(pw, flush=True)
            if outfile:
                outfile.write(pw + "\n")
                outfile.flush()
            time.sleep(delay)
    finally:
        if outfile:
            outfile.close()

if __name__ == "__main__":
    main()
