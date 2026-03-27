import numpy as np
import matplotlib.pyplot as plt

# -----------------------------
# PARAMETRELER
# -----------------------------
NUM_PARTICLES = 200
TIME_STEPS = 500
DT = 0.01

# Manyetik alan (basitleştirilmiş toroidal alan)
B0 = 5.0  # Tesla

# -----------------------------
# PARÇACIK BAŞLANGIÇLARI
# -----------------------------
positions = np.random.uniform(-1, 1, (NUM_PARTICLES, 3))
velocities = np.random.uniform(-0.5, 0.5, (NUM_PARTICLES, 3))

charge = 1.0
mass = 1.0

# -----------------------------
# MANYETİK ALAN FONKSİYONU
# -----------------------------
def magnetic_field(pos):
    x, y, z = pos
    r = np.sqrt(x**2 + y**2) + 1e-5

    # Toroidal alan (çevresel)
    Bx = -y / r * B0
    By = x / r * B0
    Bz = 0

    return np.array([Bx, By, Bz])

# -----------------------------
# SİMÜLASYON
# -----------------------------
trajectory = []

for t in range(TIME_STEPS):
    new_positions = []
    
    for i in range(NUM_PARTICLES):
        B = magnetic_field(positions[i])
        
        # Lorentz kuvveti: F = q(v x B)
        force = charge * np.cross(velocities[i], B)
        
        # ivme
        acceleration = force / mass
        
        # hız ve konum güncelle
        velocities[i] += acceleration * DT
        positions[i] += velocities[i] * DT
        
        new_positions.append(positions[i].copy())
    
    trajectory.append(new_positions)

trajectory = np.array(trajectory)

# -----------------------------
# GÖRSELLEŞTİRME
# -----------------------------
fig = plt.figure()
ax = fig.add_subplot(projection='3d')

for i in range(min(20, NUM_PARTICLES)):
    ax.plot(
        trajectory[:, i, 0],
        trajectory[:, i, 1],
        trajectory[:, i, 2]
    )

ax.set_title("Toroidal Plasma Particle Motion")
plt.show()
