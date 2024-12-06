/* I would like to implement an encryption program in Rust, that finds the first element in the input string that has a ASCII code, store it, and shift all character that has ASCII code with that number. For the ones without ASCII code, replace their index with 'x', and store their value and index in a map, for decryption use. */

/* I will implement a struct that stores the input text in string, encrypted text in string as well, and an int32 storing the ASCII number, and a map storing all characters unable to be represented as ASCII as keys, and their corresponding index as values.
Also, their will be two functions, one encrypts the text, and one decrypts it. */

use std::collections::HashMap;

struct Encryptor {
    input_text: String,
    encrypted_text: String,
    shift_value: i32,
    non_ascii_map: HashMap<usize, char>,
}

impl Encryptor {
    fn new(input_text: String) -> Self {
        Self {
            input_text,
            encrypted_text: String::new(),
            shift_value: 0,
            non_ascii_map: HashMap::new(),
        }
    }

    fn encrypt(&mut self) {
        let mut shift_set = false;

        for (index, ch) in self.input_text.chars().enumerate() {
            if ch.is_ascii() {
                if !shift_set {
                    self.shift_value = ch as i32;
                    shift_set = true;
                }
                let shifted_char = ((ch as u8).wrapping_add(self.shift_value as u8)) % 128;
                self.encrypted_text.push(shifted_char as char);
            } else {
                self.encrypted_text.push('x');
                self.non_ascii_map.insert(index, ch);
            }
        }
    }

    fn decrypt(&self) -> String {
        let mut decrypted_text = String::new();

        for (index, ch) in self.encrypted_text.chars().enumerate() {
            if ch == 'x' {
                if let Some(&original_char) = self.non_ascii_map.get(&index) {
                    decrypted_text.push(original_char);
                }
            } else if ch.is_ascii() {
                let shifted_char = ((ch as u8).wrapping_sub(self.shift_value as u8)) % 128;
                decrypted_text.push(shifted_char as char);
            }
        }

        decrypted_text
    }
}

fn main() {
    let input_text = String::from("Hello, 世界!"); 
    let mut encryptor = Encryptor::new(input_text);

    encryptor.encrypt();
    println!("Encrypted text: {}", encryptor.encrypted_text);
    println!("Shift value: {}", encryptor.shift_value);
    println!("Non-ASCII map: {:?}", encryptor.non_ascii_map);

    let decrypted_text = encryptor.decrypt();
    println!("Decrypted text: {}", decrypted_text);
}
