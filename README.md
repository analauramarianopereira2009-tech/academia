import React, { useState, useEffect } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableOpacity,
  TextInput,
  SafeAreaView,
  StatusBar,
  KeyboardAvoidingView,
  Platform,
  TouchableWithoutFeedback,
  Keyboard,
} from 'react-native';

export default function App() {
  // Estado do Cronómetro
  const [secondsLeft, setSecondsLeft] = useState(90);
  const [isTimerActive, setIsTimerActive] = useState(false);

  // Estado dos Inputs do Exercício
  const [weight, setWeight] = useState('60');
  const [reps, setReps] = useState('10');
  const [completedSets, setCompletedSets] = useState(0);

  // Lógica do Cronómetro
  useEffect(() => {
    let interval: NodeJS.Timeout;

    if (isTimerActive && secondsLeft > 0) {
      interval = setInterval(() => {
        setSecondsLeft((prev) => prev - 1);
      }, 1000);
    } else if (secondsLeft === 0) {
      setIsTimerActive(false);
    }

    return () => clearInterval(interval);
  }, [isTimerActive, secondsLeft]);

  // Função para iniciar o descanso
  const startRestTimer = (seconds: number = 90) => {
    setSecondsLeft(seconds);
    setIsTimerActive(true);
  };

  // Função para concluir a série
  const handleCompleteSet = () => {
    setCompletedSets((prev) => prev + 1);
    startRestTimer(90); // Inicia descanso automático de 90s
    Keyboard.dismiss();
  };

  // Formatação do tempo (01:30)
  const formatTime = (totalSeconds: number) => {
    const mins = Math.floor(totalSeconds / 60);
    const secs = totalSeconds % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
  };

  return (
    <SafeAreaView style={styles.container}>
      <StatusBar barStyle="light-content" backgroundColor="#121212" />
      <TouchableWithoutFeedback onPress={Keyboard.dismiss}>
        <KeyboardAvoidingView
          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
          style={styles.inner}
        >
          {/* Cabeçalho */}
          <View style={styles.header}>
            <Text style={styles.headerSubtitle}>TREINO A — PEITO E TRÍCEPS</Text>
            <Text style={styles.headerTitle}>Supino Reto com Barra</Text>
          </View>

          {/* Card do Cronómetro de Descanso */}
          <View style={styles.timerCard}>
            <Text style={styles.timerLabel}>TEMPO DE DESCANSO</Text>
            <Text style={styles.timerDisplay}>{formatTime(secondsLeft)}</Text>
            
            <View style={styles.timerButtonsRow}>
              <TouchableOpacity
                style={styles.timerQuickBtn}
                onPress={() => setSecondsLeft((prev) => prev + 30)}
              >
                <Text style={styles.timerQuickBtnText}>+30s</Text>
              </TouchableOpacity>

              <TouchableOpacity
                style={[styles.timerQuickBtn, styles.timerStopBtn]}
                onPress={() => setIsTimerActive(false)}
              >
                <Text style={styles.timerQuickBtnText}>Pausar</Text>
              </TouchableOpacity>
            </View>
          </View>

          {/* Card de Registo da Série */}
          <View style={styles.exerciseCard}>
            <View style={styles.badgeRow}>
              <Text style={styles.badgeText}>SÉRIE ATIVA: #{completedSets + 1}</Text>
              <Text style={styles.badgeCompleted}>Concluídas: {completedSets}</Text>
            </View>

            <View style={styles.inputRow}>
              <View style={styles.inputGroup}>
                <Text style={styles.inputLabel}>Carga (kg)</Text>
                <TextInput
                  style={styles.input}
                  keyboardType="numeric"
                  value={weight}
                  onChangeText={setWeight}
                  placeholderTextColor="#666"
                />
              </View>

              <View style={styles.inputGroup}>
                <Text style={styles.inputLabel}>Repetições</Text>
                <TextInput
                  style={styles.input}
                  keyboardType="numeric"
                  value={reps}
                  onChangeText={setReps}
                  placeholderTextColor="#666"
                />
              </View>
            </View>

            <TouchableOpacity style={styles.mainButton} onPress={handleCompleteSet}>
              <Text style={styles.mainButtonText}>✓ CONCLUIR SÉRIE</Text>
            </TouchableOpacity>
          </View>
        </KeyboardAvoidingView>
      </TouchableWithoutFeedback>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#121212',
  },
  inner: {
    flex: 1,
    padding: 20,
    justifyContent: 'space-between',
  },
  header: {
    marginTop: 10,
  },
  headerSubtitle: {
    color: '#00E676',
    fontSize: 12,
    fontWeight: 'bold',
    letterSpacing: 1,
  },
  headerTitle: {
    color: '#FFFFFF',
    fontSize: 26,
    fontWeight: 'bold',
    marginTop: 4,
  },
  timerCard: {
    backgroundColor: '#1E1E1E',
    borderRadius: 20,
    padding: 24,
    alignItems: 'center',
    borderWidth: 2,
    borderColor: '#00E676',
    shadowColor: '#00E676',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 8,
    elevation: 5,
  },
  timerLabel: {
    color: '#B0B0B0',
    fontSize: 12,
    fontWeight: 'bold',
    letterSpacing: 1.5,
  },
  timerDisplay: {
    color: '#FFFFFF',
    fontSize: 56,
    fontWeight: 'bold',
    marginVertical: 8,
  },
  timerButtonsRow: {
    flexDirection: 'row',
    gap: 12,
    marginTop: 8,
  },
  timerQuickBtn: {
    backgroundColor: '#2A2A2A',
    paddingHorizontal: 16,
    paddingVertical: 8,
    borderRadius: 20,
  },
  timerStopBtn: {
    backgroundColor: '#331111',
  },
  timerQuickBtnText: {
    color: '#FFFFFF',
    fontSize: 12,
    fontWeight: 'bold',
  },
  exerciseCard: {
    backgroundColor: '#1E1E1E',
    borderRadius: 20,
    padding: 20,
    marginBottom: 10,
  },
  badgeRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 20,
  },
  badgeText: {
    color: '#FF6D00',
    fontWeight: 'bold',
    fontSize: 14,
  },
  badgeCompleted: {
    color: '#B0B0B0',
    fontSize: 14,
  },
  inputRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 20,
  },
  inputGroup: {
    width: '47%',
  },
  inputLabel: {
    color: '#B0B0B0',
    fontSize: 12,
    marginBottom: 8,
    fontWeight: '600',
  },
  input: {
    backgroundColor: '#2A2A2A',
    color: '#FFFFFF',
    borderRadius: 12,
    padding: 16,
    fontSize: 22,
    fontWeight: 'bold',
    textAlign: 'center',
    borderWidth: 1,
    borderColor: '#333333',
  },
  mainButton: {
    backgroundColor: '#FF6D00',
    padding: 18,
    borderRadius: 12,
    alignItems: 'center',
    shadowColor: '#FF6D00',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.4,
    shadowRadius: 6,
    elevation: 4,
  },
  mainButtonText: {
    color: '#FFFFFF',
    fontWeight: 'bold',
    fontSize: 16,
    letterSpacing: 1,
  },
});
