import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

# =====================================
# PARAMETRELER
# =====================================
NUM_PARTICLES = 200
TIME_STEPS = 500
DT = 0.01

CHARGE = 1.0
MASS = 1.0

B0 = 5.0  # Toroidal manyetik alan gücü

# =====================================
# BAŞLANGIÇ
# =====================================
def initialize_particles():
    positions = np.random.uniform(-1, 1, (NUM_PARTICLES, 3))
    velocities = np.random.uniform(-0.5, 0.5, (NUM_PARTICLES, 3))
    return positions, velocities

# =====================================
# MANYETİK ALAN (TOROIDAL)
# =====================================
def magnetic_field(pos):
    x, y, z = pos
    r = np.sqrt(x**2 + y**2) + 1e-8

    # Toroidal alan (çembersel)
    Bx = -y / r * B0
    By = x / r * B0
    Bz = 0.0

    return np.array([Bx, By, Bz])

# =====================================
# SİMÜLASYON
# =====================================
def simulate():
    positions, velocities = initialize_particles()
    trajectory = []

    for t in range(TIME_STEPS):
        new_positions = []

        for i in range(NUM_PARTICLES):
            B = magnetic_field(positions[i])

            # Lorentz kuvveti
            force = CHARGE * np.cross(velocities[i], B)
            acceleration = force / MASS

            # Güncelleme
            velocities[i] += acceleration * DT
            positions[i] += velocities[i] * DT

            new_positions.append(positions[i].copy())

        trajectory.append(new_positions)

    return np.array(trajectory)

# =====================================
# GÖRSELLEŞTİRME
# =====================================
def plot_trajectory(trajectory):
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')

    num_show = min(20, NUM_PARTICLES)

    for i in range(num_show):
        ax.plot(
            trajectory[:, i, 0],
            trajectory[:, i, 1],
            trajectory[:, i, 2]
        )

    ax.set_title("Toroidal Plasma Simulation")
    ax.set_xlabel("X")
    ax.set_ylabel("Y")
    ax.set_zlabel("Z")

    plt.show()

# =====================================
# MAIN
# =====================================
if __name__ == "__main__":
    print("Simülasyon başlatılıyor...")
    traj = simulate()
    print("Simülasyon tamamlandı.")
    plot_trajectory(traj)
