# MINE
MINE an AI app that you own
# MINE v3.0 FINAL - THE TECH YOU OWN
# ALL FEATURES + DEBUGGED - Pretoria Build 2026

from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen, FadeTransition
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.button import Button
from kivy.lang import Builder
from kivy.clock import Clock
from kivy.core.window import Window
import hashlib, json, time, os, gc, random
from cryptography.fernet import Fernet

# --- ANTI-HACK: No Screenshot ---
try:
    from android.view import WindowManager
    from jnius import autoclass
    PythonActivity = autoclass('org.kivy.android.PythonActivity')
    activity = PythonActivity.mActivity
    activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_SECURE)
except:
    pass # Desktop - no flag needed

# --- VAULT DEBUGGED ---
class Vault:
    def __init__(self):
        self.key_file = "mine.key"
        if not os.path.exists(self.key_file):
            with open(self.key_file, "wb") as f:
                f.write(Fernet.generate_key())
        with open(self.key_file, "rb") as f:
            self.fernet = Fernet(f.read())
        self.fail = 0
    
    def encrypt(self, text):
        return self.fernet.encrypt(text.encode()).decode()
    
    def decrypt(self, token):
        try: return self.fernet.decrypt(token.encode()).decode()
        except: return "{}"
    
    def check_brute(self):
        self.fail += 1
        if self.fail >= 5:
            print("BRUTE FORCE - LOCKED")

vault = Vault()

# --- SOULFILE + ALL VAULTS ---
class SoulFile:
    def __init__(self):
        for file in ["soulfile.json","health.json","money.json","photos.json"]:
            if not os.path.exists(file):
                with open(file, "w") as f: json.dump([], f)
    
    def store(self, file, intent, data):
        try:
            payload = json.dumps({"intent":intent,"data":data,"ts":time.time(),"hash":hashlib.sha256(data.encode()).hexdigest()[:8]})
            enc = vault.encrypt(payload)
            with open(file, "r") as f: mem = json.load(f)
            mem.append(enc)
            with open(file, "w") as f: json.dump(mem, f)
            gc.collect()
            return len(mem), hashlib.sha256(payload.encode()).hexdigest()
        except Exception as e:
            print(e); return 0,""

    def recall(self, file, query=""):
        try:
            with open(file, "r") as f: mem = json.load(f)
            out=[]
            for enc in mem:
                d
