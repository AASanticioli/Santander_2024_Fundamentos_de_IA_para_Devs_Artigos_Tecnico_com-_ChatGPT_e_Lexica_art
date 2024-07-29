# Uma Arquitetura Alternativa em Seu Projeto Android
A arquitetura limpa, popularizada por Robert C. Martin e adaptada por Tom Hombergs em seu livro “Get Your Hands Dirty on Clean Architecture”, oferece uma abordagem estruturada para o desenvolvimento de software, separando claramente as responsabilidades e facilitando a manutenção e a escalabilidade do código. Neste artigo, aplicaremos esses conceitos em um projeto Android utilizando Kotlin, Jetpack Compose, Gradle e Hilt.

## Conceitos Fundamentais da Arquitetura Limpa
A arquitetura limpa é baseada em princípios de design que promovem a separação de responsabilidades e a independência de frameworks e bibliotecas. Ela é composta por várias camadas, cada uma com uma responsabilidade específica:

1. **Camada de Entidades**: Contém as regras de negócio mais gerais e independentes.
2. **Camada de Casos de Uso**: Define as operações específicas do aplicativo, utilizando as entidades.
3. **Camada de Interface de Usuário**: Responsável pela apresentação dos dados e interação com o usuário.
4. **Camada de Implementação**: Contém as implementações concretas de interfaces e dependências externas.


**Arquitetura Limpa**: É uma abordagem de design de software que promove a separação de responsabilidades e a independência de frameworks e bibliotecas, facilitando a manutenção e a escalabilidade do código.

## Aplicação em Projetos Android
Para implementar a arquitetura limpa em um projeto Android, utilizaremos Kotlin, Jetpack Compose para a interface de usuário, Gradle para o gerenciamento de dependências e Hilt para a injeção de dependências.

**Kotlin**: É uma linguagem de programação moderna e concisa, recomendada para o desenvolvimento Android.

**Jetpack Compose**: É um kit de ferramentas moderno para a criação de interfaces de usuário de forma declarativa.

**Gradle**: É a ferramenta padrão para o gerenciamento de dependências e automação de builds em projetos Android.

**Hilt**: É uma biblioteca para injeção de dependências que simplifica a integração com outros componentes do Jetpack.

## Exemplo Prático
Vamos criar um aplicativo de gerenciamento de tarefas que segue a arquitetura limpa. O projeto será dividido em módulos correspondentes às camadas da arquitetura.

### build.gradle (Projeto)
```
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
    id 'dagger.hilt.android.plugin'
    kotlin("kapt")
}

android {
    compileSdkVersion 33
    defaultConfig {
        applicationId "com.example.cleanarchitecture"
        minSdkVersion 21
        targetSdkVersion 33
        versionCode 1
        versionName "1.0"
    }
    buildFeatures {
        compose true
    }
    composeOptions {
        kotlinCompilerExtensionVersion "1.4.2"
    }
}

dependencies {
    implementation "androidx.core:core-ktx:1.9.0"
    implementation "androidx.lifecycle:lifecycle-runtime-ktx:2.5.1"
    implementation "androidx.activity:activity-compose:1.6.1"
    implementation "androidx.compose.ui:ui"
    implementation "androidx.compose.material:material"
    implementation "androidx.compose.ui:ui-tooling-preview"
    implementation "com.google.dagger:hilt-android:2.45"
    kapt "com.google.dagger:hilt-android-compiler:2.45"
}
```

### MainActivity.kt
```
package com.example.cleanarchitecture

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.hilt.navigation.compose.hiltViewModel
import com.example.cleanarchitecture.ui.TaskScreen
import dagger.hilt.android.AndroidEntryPoint

@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            TaskScreen()
        }
    }
}
```

### TaskScreen.kt
```
package com.example.cleanarchitecture.ui

import androidx.compose.material.*
import androidx.compose.runtime.Composable
import androidx.hilt.navigation.compose.hiltViewModel
import com.example.cleanarchitecture.viewmodel.TaskViewModel

@Composable
fun TaskScreen(viewModel: TaskViewModel = hiltViewModel()) {
    val tasks = viewModel.tasks
    Scaffold(
        topBar = {
            TopAppBar(title = { Text("Task Manager") })
        },
        content = {
            TaskList(tasks)
        }
    )
}
```

### TaskViewModel.kt
```
package com.example.cleanarchitecture.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import com.example.cleanarchitecture.domain.Task
import com.example.cleanarchitecture.domain.TaskRepository
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.launch
import javax.inject.Inject

@HiltViewModel
class TaskViewModel @Inject constructor(
    private val repository: TaskRepository
) : ViewModel() {
    val tasks = repository.getTasks()

    fun addTask(task: Task) {
        viewModelScope.launch {
            repository.addTask(task)
        }
    }
}
```

### TaskRepository.kt
```
package com.example.cleanarchitecture.domain

import kotlinx.coroutines.flow.Flow

interface TaskRepository {
    fun getTasks(): Flow<List<Task>>
    suspend fun addTask(task: Task)
}
```

### Task.kt
```
package com.example.cleanarchitecture.domain

data class Task(
    val id: Int,
    val title: String,
    val description: String
)
```

## Conclusão
A aplicação da arquitetura limpa em projetos Android utilizando Kotlin, Jetpack Compose, Gradle e Hilt proporciona uma estrutura robusta e escalável, facilitando a manutenção e a evolução do software. A separação clara de responsabilidades e a independência de frameworks são benefícios significativos dessa abordagem.

## Referências
* Hombergs, T. (2019). Get Your Hands Dirty on Clean Architecture. Apress.
* Google. (2023). Android Developers. Disponível em: developer.android.com
* Freeman, E., & Robson, E. (2014). Head First Android Development. O’Reilly Media.