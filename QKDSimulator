import random
import hashlib
from qiskit import QuantumCircuit, Aer, execute
import tkinter as tk
from tkinter import ttk

class QKDSimulator:
    def __init__(self, root):
        self.qubit_length = 254
        self.circuit = QuantumCircuit(self.qubit_length, self.qubit_length)

        self.root = root
        self.message_entry = None
        self.encrypt_button = None
        self.encrypted_label = None
        self.decrypted_label = None

        self.create_gui()

    def create_gui(self):
        self.root.title("Quantum Key Distribution (QKD) Simulator")

        # Configure style
        style = ttk.Style()
        style.configure("TLabel", font=("Arial", 12))
        style.configure("TButton", font=("Arial", 12))

        # Create message entry widget
        entry_frame = ttk.Frame(self.root, padding=10)
        entry_frame.pack()

        message_label = ttk.Label(entry_frame, text="Enter your message:", font=("Arial", 12, "bold"))
        message_label.pack(side="left", padx=(0, 10))

        self.message_entry = ttk.Entry(entry_frame, width=50, font=("Arial", 12))
        self.message_entry.pack(side="left", fill="x", expand=True)
        self.message_entry.bind("<Return>", lambda event: self.encrypt_message())

        # Create Enter button
        enter_frame = ttk.Frame(self.root, padding=10)
        enter_frame.pack()

        self.enter_button = ttk.Button(enter_frame, text="Enter", command=self.encrypt_message, style="Custom.TButton")
        self.enter_button.pack()

        # Create labels for displaying encrypted and decrypted messages
        encrypted_frame = ttk.Frame(self.root, padding=10)
        encrypted_frame.pack()

        encrypted_label = ttk.Label(encrypted_frame, text="Encrypted Message:", font=("Arial", 12, "bold"))
        encrypted_label.pack()

        self.encrypted_label = ttk.Label(encrypted_frame, text="", font=("Arial", 12))
        self.encrypted_label.pack()

        decrypted_frame = ttk.Frame(self.root, padding=10)
        decrypted_frame.pack()

        decrypted_label = ttk.Label(decrypted_frame, text="Decrypted Message:", font=("Arial", 12, "bold"))
        decrypted_label.pack()

        self.decrypted_label = ttk.Label(decrypted_frame, text="", font=("Arial", 12))
        self.decrypted_label.pack()

        # Bind Enter key press event to the Enter button's command
        self.root.bind("<Return>", lambda event: self.enter_button.invoke())

    def encrypt_message(self):
        message = self.message_entry.get()

        key = self.generate_random_key()
        self.prepare_qubits(key)

        alicia_bases = [random.choice(['X', 'Z']) for _ in range(self.qubit_length)]
        david_bases = [random.choice(['X', 'Z']) for _ in range(self.qubit_length)]

        alicia_key = self.measure_qubits()

        sifted_indices = self.compare_bases(alicia_bases, david_bases)
        sifted_key = self.sift_key(alicia_key, sifted_indices)

        self.detect_eavesdropping(alicia_bases, david_bases, sifted_key)

        error_corrected_key = self.error_correction(sifted_key)
        privacy_amplified_key = self.privacy_amplification(error_corrected_key)

        message_bits = self.classical_communication(message)
        encrypted_message = self.encrypt_message_bits(message_bits, privacy_amplified_key)
        encrypted_message_ascii = self.binary_to_ascii(encrypted_message)
        decrypted_message = self.decrypt_message(encrypted_message, privacy_amplified_key)
        decrypted_message_ascii = self.binary_to_ascii(decrypted_message)

        self.encrypted_label.config(text=encrypted_message_ascii)
        self.decrypted_label.config(text=decrypted_message_ascii)

    def apply_quantum_gates(self):
        self.circuit.h(range(self.qubit_length))
        self.circuit.barrier()

        self.circuit.x(1)
        self.circuit.barrier()

        self.circuit.y(2)
        self.circuit.barrier()

        self.circuit.z(3)
        self.circuit.barrier()

    def measure_qubits(self):
        self.circuit.measure_all()
        simulator = Aer.get_backend('qasm_simulator')
        job = execute(self.circuit, simulator, shots=1)
        result = job.result()
        counts = result.get_counts(self.circuit)
        return list(counts.keys())[0]

    def generate_random_key(self):
        key = [random.choice([0, 1]) for _ in range(self.qubit_length)]
        return key

    def prepare_qubits(self, key):
        for i, bit in enumerate(key):
            if bit == 1:
                self.circuit.x(i)

        self.apply_quantum_gates()

    def exchange_qubits_with_noise(self, qubits):
        noisy_qubits = qubits.copy()
        for i in range(len(noisy_qubits)):
            error_bit = random.choice([0, 1])
            if error_bit == 1:
                noisy_qubits[i] = (noisy_qubits[i] + 1) % 2
        return noisy_qubits

    def compare_bases(self, alicia_bases, david_bases):
        matching_bases = [i for i in range(len(alicia_bases)) if alicia_bases[i] == david_bases[i]]
        return matching_bases

    def sift_key(self, key, indices):
        sifted_key = [key[i] for i in indices]
        return sifted_key

    def detect_eavesdropping(self, alicia_bases, david_bases, sifted_key):
        matching_bases = self.compare_bases(alicia_bases, david_bases)
        sifted_key_length = len(sifted_key)
        if len(matching_bases) != sifted_key_length:
            print("Potential eavesdropping detected!")
        else:
            print("No evidence of eavesdropping detected.")

    def classical_communication(self, message):
        message_ascii = [ord(char) for char in message]
        message_bits = []
        for ascii_val in message_ascii:
            binary_val = bin(ascii_val)[2:].zfill(8)
            message_bits.extend([int(bit) for bit in binary_val])
        return message_bits

    def encrypt_message_bits(self, message, key):
        message_bits = [int(bit) for bit in message]
        key_length = len(key)
        ciphertext = []
        for i, bit in enumerate(message_bits):
            key_bit = key[i % key_length]  # Handle message longer than key
            encrypted_bit = (bit + key_bit) % 2
            ciphertext.append(encrypted_bit)
        return ciphertext

    def decrypt_message(self, ciphertext, key):
        key_length = len(key)
        message = []
        for i, bit in enumerate(ciphertext):
            key_bit = key[i % key_length]  # Handle ciphertext longer than key
            decrypted_bit = (bit - key_bit) % 2  # Modified decryption operation
            message.append(decrypted_bit)
        return message

    def binary_to_ascii(self, message):
        ascii_chars = []
        for i in range(0, len(message), 8):
            binary_val = ''.join(str(bit) for bit in message[i:i+8])
            ascii_char = chr(int(binary_val, 2))
            ascii_chars.append(ascii_char)
        return ''.join(ascii_chars)

    def error_correction(self, sifted_key):
        # Repetition code error correction
        error_corrected_key = []
        for bit in sifted_key:
            count = sifted_key.count(bit)
            if count > len(sifted_key) // 2:
                error_corrected_key.append(bit)
        return error_corrected_key

    def privacy_amplification(self, sifted_key):
        # Privacy amplification using hash function
        sifted_key_str = ''.join(str(bit) for bit in sifted_key)
        hash_key = hashlib.sha256(sifted_key_str.encode()).digest()
        privacy_amplified_key = [int(bit) for bit in hash_key]
        return privacy_amplified_key

    def run(self):
        self.root.mainloop()

if __name__ == '__main__':
    root = tk.Tk()
    qkd_simulator = QKDSimulator(root)
    qkd_simulator.run()
