# IGlaba2-
##### Lab2_inj
##### Вторая лабораторная работа по компьютерной графике.
##### Выполнил: Исламгалеев Денис
##### Группа: МО-221
## Было сделано:
### Перемещение треугольника по координате X:
#### Создаём глобальную переменную gWorldLocation:
##### GLuint gWorldLocation;
#### Добавляем код шейдера
##### // Версия шейдера 3.3                                                      
##### #version 330                                                               
##### // Входной тип данных - vec3 в позиции 0                                   
##### layout (location = 0) in vec3 Position;
##### // uniform-переменная типа mat4                                            
##### uniform mat4 gWorld;
##### void main() {
##### // Умножаем вектор вершин на всемирную матрицу для смещения треугольника
##### gl_Position = gWorld * vec4(Position, 1.0);
##### }
#### В функции рендера мы подготавливаем матрицу 4x4
##### // Переменная для изменения значения X
##### static float Scale = -1.0f;
##### 
##### // С каждой отрисовкой увеличиваем Scale
##### Scale += 0.001f;
##### glm::mat4 World;
##### World[0][0] = 1.0f; World[0][1] = 0.0f; World[0][2] = 0.0f; World[0][3] = sinf(Scale);
##### World[1][0] = 0.0f; World[1][1] = 1.0f; World[1][2] = 0.0f; World[1][3] = 0.0f;
##### World[2][0] = 0.0f; World[2][1] = 0.0f; World[2][2] = 1.0f; World[2][3] = 0.0f;
##### World[3][0] = 0.0f; World[3][1] = 0.0f; World[3][2] = 0.0f; World[3][3] = 1.0f;
#### Загружаем матрицу 4х4
##### //Первый параметр - это адрес uniform-переменной (находится после компиляции шейдера используя glGetUniformLocation()). 
##### //Второй параметр - это количество матриц, значения которых мы обновляем. 
##### //Наш третий параметр в glUniformMatrix4fv() - это GL_TRUE, потому что мы поставляем матрицу упорядоченную по строкам.
##### //Четвертый параметр - это просто указатель на первый элемент матрицы
##### glUniformMatrix4fv(gWorldLocation, 1, GL_TRUE, &World.m[0][0]);
#### Добавляем функции
##### // Функция, добавляющая шейдер к программе
##### void addShader(GLuint shaderProgram, const std::string pShaderText, GLenum shaderType) {
##### // Создаём шейдер
##### GLuint shaderObj = glCreateShader(shaderType);
##### if (shaderObj == 0) {
##### std::cerr << "Error: creating shader type " << shaderType << std::endl;
##### exit(1);
##### }
##### // Сохраняем код шейдера в массиве
##### const GLchar *p[1];
##### p[0] = pShaderText.c_str();
##### // Массив длин кодов шейдеров
##### GLint lengths[1];
##### lengths[0] = pShaderText.length();
##### // Задаём исходники шейдера
##### ##### glShaderSource(shaderObj, 1, p, lengths);
##### // Компилируем шейдер
##### glCompileShader(shaderObj);
##### // Проверяем, что шейдер успешно скомпилировался
##### GLint success;
##### glGetShaderiv(shaderObj, GL_COMPILE_STATUS, &success);
##### if (!success) {
##### GLchar infoLog[1024];
##### glGetShaderInfoLog(shaderObj, sizeof(infoLog), nullptr, infoLog);
##### std::cerr << "Error compiling shader type " << shaderType << ": '" << infoLog << "'" << std::endl;
##### exit(1);
##### }
##### // Добавляем шейдер в программу
##### glAttachShader(shaderProgram, shaderObj);
##### }
##### // Функция, компилирующая программу-шейдер
##### void compileShaders() {
##### // Создаём программу-шейдер
##### GLuint shaderProgram = glCreateProgram();
##### if (shaderProgram == 0) {
##### std::cerr << "Error creating shader program" << std::endl;
##### exit(1);
##### }
##### // Добавляем шейдер для вершин
##### addShader(shaderProgram, pVS, GL_VERTEX_SHADER);
##### GLint success = 0;
##### GLchar errorLog[1024] = { 0 };
##### // Линкуем программу
##### glLinkProgram(shaderProgram);
##### // Проверяем, что линковка прошла успешно
##### glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
##### if (success == 0) {
##### glGetProgramInfoLog(shaderProgram, sizeof(errorLog), nullptr, errorLog);
##### std::cerr << "Error linking shader program: '" << errorLog << "'" << std::endl;
##### exit(1);
##### }
##### // Валидируем программу
##### glValidateProgram(shaderProgram);
##### // Проверяем, что валидация прошла успешно
##### glGetProgramiv(shaderProgram, GL_VALIDATE_STATUS, &success);
##### if (success == 0) {
##### glGetProgramInfoLog(shaderProgram, sizeof(errorLog), nullptr, errorLog);
##### std::cerr << "Invalid shader program: '" << errorLog << "'" << std::endl;
##### exit(1);
##### }
##### // Указываем OpenGL, что надо использовать эту программу
##### glUseProgram(shaderProgram);
##### // Сохраняем местоположение переменной gWorld
##### gWorldLocation = glGetUniformLocation(shaderProgram, "gWorld");
##### assert(gWorldLocation != 0xFFFFFFFF);
##### } 
### Вращение треугольника:
#### В функции отрисовки меняем матрицу World
##### World[0][0] = cosf(Scale); World[0][1] = -sinf(Scale); World[0][2] = 0.0f; World[0][3] = 0.0f;
##### World[1][0] = sinf(Scale); World[1][1] = cosf(Scale);  World[1][2] = 0.0f; World[1][3] = 0.0f;
##### World[2][0] = 0.0f;        World[2][1] = 0.0f;         World[2][2] = 1.0f; World[2][3] = 0.0f;
##### World[3][0] = 0.0f;		   World[3][1] = 0.0f;         World[3][2] = 0.0f; World[3][3] = 1.0f;
### Преобразование масштаба треугольника:
#### В функции отрисовки меняем матрицу World
##### World[0][0]=sinf(Scale); World[0][1]=0.0f;        World[0][2]=0.0f;        World[0][3]=0.0f;
##### World[1][0] = 0.0f;        World[1][1] = cosf(Scale); World[1][2] = 0.0f;        World[1][3] = 0.0f;
##### World[2][0] = 0.0f;        World[2][1] = 0.0f;        World[2][2] = sinf(Scale); World[2][3] = 0.0f;
##### World[3][0] = 0.0f;        World[3][1] = 0.0f;        World[3][2] = 0.0f;        World[3][3] = 1.0f;
### Обьединение преобразований:
#### В функции отрисовки меняем матрицу World
#### Создаём класс, необходимый для преобразований и определяем в нем методы:изменения мастштаба, поворота, положение, получения итоговой матрицы, инициализации матриц
 
#####     class Pipeline
#####     {
#####     public:
#####         Pipeline()
#####         {
#####             m_scale = glm::vec3(1.0f, 1.0f, 1.0f);
#####             m_worldPos = glm::vec3(0.0f, 0.0f, 0.0f);
#####             m_rotateInfo = glm::vec3(0.0f, 0.0f, 0.0f);
#####             
#####         }

#####         //функции задания изменения масштаба
#####        void Scale(float ScaleX, float ScaleY, float ScaleZ)
#####         {
#####             m_scale.x = ScaleX;
 #####            m_scale.y = ScaleY;
#####             m_scale.z = ScaleZ;
#####         }

#####         // Функция задания изменения положения
#####         void WorldPos(float x, float y, float z)
#####         {
#####             m_worldPos.x = x;
#####             m_worldPos.y = y;
 #####            m_worldPos.z = z;
#####         }

#####         // Функция задания вращения
#####         void Rotate(float RotateX, float RotateY, float RotateZ)
#####         {
#####             m_rotateInfo.x = RotateX;
#####             m_rotateInfo.y = RotateY;
#####             m_rotateInfo.z = RotateZ;
#####         }
##### 
 #####        const glm::mat4* getTransformation()
#####         {
#####             glm::mat4 ScaleTrans, RotateTrans, TranslationTrans;
#####             InitScaleTransform(ScaleTrans);
#####             InitRotateTransform(RotateTrans);
#####             InitTranslationTransform(TranslationTrans);
#####             m_transformation = TranslationTrans * RotateTrans * ScaleTrans;
#####             return &m_transformation;
#####         }
#####         void InitScaleTransform(glm::mat4& m) const
#####         {
#####             m[0][0] = m_scale.x; m[0][1] = 0.0f; m[0][2] = 0.0f; m[0][3] = 0.0f;
#####             m[1][0] = 0.0f; m[1][1] = m_scale.y; m[1][2] = 0.0f; m[1][3] = 0.0f;
#####             m[2][0] = 0.0f; m[2][1] = 0.0f; m[2][2] = m_scale.z; m[2][3] = 0.0f;
#####             m[3][0] = 0.0f; m[3][1] = 0.0f; m[3][2] = 0.0f; m[3][3] = 1.0f;
#####         }
##### 
#####         void InitRotateTransform(glm::mat4& m) const
#####         {
#####             glm::mat4 rx, ry, rz;
##### 
#####             const float x = ToRadian(m_rotateInfo.x);
#####             const float y = ToRadian(m_rotateInfo.y);
#####             const float z = ToRadian(m_rotateInfo.z);
##### 
#####             rx[0][0] = 1.0f; rx[0][1] = 0.0f; rx[0][2] = 0.0f; rx[0][3] = 0.0f;
#####             rx[1][0] = 0.0f; rx[1][1] = cosf(x); rx[1][2] = -sinf(x); rx[1][3] = 0.0f;
#####             rx[2][0] = 0.0f; rx[2][1] = sinf(x); rx[2][2] = cosf(x); rx[2][3] = 0.0f;
#####             rx[3][0] = 0.0f; rx[3][1] = 0.0f; rx[3][2] = 0.0f; rx[3][3] = 1.0f;

#####             ry[0][0] = cosf(y); ry[0][1] = 0.0f; ry[0][2] = -sinf(y); ry[0][3] = 0.0f;
#####             ry[1][0] = 0.0f; ry[1][1] = 1.0f; ry[1][2] = 0.0f; ry[1][3] = 0.0f;
#####             ry[2][0] = sinf(y); ry[2][1] = 0.0f; ry[2][2] = cosf(y); ry[2][3] = 0.0f;
#####             ry[3][0] = 0.0f; ry[3][1] = 0.0f; ry[3][2] = 0.0f; ry[3][3] = 1.0f;

#####             rz[0][0] = cosf(z); rz[0][1] = -sinf(z); rz[0][2] = 0.0f; rz[0][3] = 0.0f;
#####             rz[1][0] = sinf(z); rz[1][1] = cosf(z); rz[1][2] = 0.0f; rz[1][3] = 0.0f;
#####             rz[2][0] = 0.0f; rz[2][1] = 0.0f; rz[2][2] = 1.0f; rz[2][3] = 0.0f;
#####             rz[3][0] = 0.0f; rz[3][1] = 0.0f; rz[3][2] = 0.0f; rz[3][3] = 1.0f;

#####             m = rz * ry * rx;
#####         }

#####         void InitTranslationTransform(glm::mat4& m) const
#####         {
#####             m[0][0] = 1.0f; m[0][1] = 0.0f; m[0][2] = 0.0f;m[0][3] = m_worldPos.x;
#####             m[1][0] = 0.0f; m[1][1] = 1.0f;m[1][2] = 0.0f;m[1][3] = m_worldPos.y;
#####             m[2][0] = 0.0f; m[2][1] = 0.0f;m[2][2] = 1.0f; m[2][3] = m_worldPos.z;
#####             m[3][0] = 0.0f; m[3][1] = 0.0f;m[3][2] = 0.0f; m[3][3] = 1.0f;
#####         }


#####     private:
    
#####         glm::vec3 m_scale;
#####         glm::vec3 m_worldPos;
#####         glm::vec3 m_rotateInfo;
#####         glm::mat4 m_transformation;
#####   };
#### В функции отрисовки создаем объект этого класса и работаем с ним
##### //Преобразования
#####     Pipeline p;
#####     p.Scale(sinf(Scale * 0.1f), sinf(Scale * 0.1f), sinf(Scale * 0.1f));
#####     p.WorldPos(sinf(Scale), 0.0f, 0.0f);
#####     p.Rotate(sinf(Scale) * 90.0f, sinf(Scale) * 90.0f, sinf(Scale) * 90.0f);
#####     glUniformMatrix4fv(gWorldLocation, 1, GL_TRUE, (const GLfloat*)p.getTransformation());
### Проекция перспективы
#### В класс Pipeline добавим метод и необходимые переменые
##### struct {
#####     float FOV;
#####     float Width;
#####     float Height;
 #####    float zNear;
#####     float zFar;
##### } m_persProj;

#####  void SetPerspectiveProj(float FOV, float Width, float Height, float zNear, float zFar)
##### {
#####     m_persProj.FOV = FOV;
#####     m_persProj.Width = Width;
#####     m_persProj.Height = Height;
#####     m_persProj.zNear = zNear;
#####     m_persProj.zFar = zFar;
##### }

##### void InitPerspectiveProj(glm::mat4& m) const 
##### {
#####     const float ar = m_persProj.Width / m_persProj.Height;
 #####    const float zNear = m_persProj.zNear;
 #####    const float zFar = m_persProj.zFar;
 #####    const float zRange = zNear - zFar;
#####     const float tanHalfFOV = tanf(ToRadian(m_persProj.FOV / 2.0));

 #####    m[0][0] = 1.0f / (tanHalfFOV * ar);
#####     m[0][1] = 0.0f;
#####     m[0][2] = 0.0f;
#####     m[0][3] = 0.0f;

#####     m[1][0] = 0.0f;
#####     m[1][1] = 1.0f / tanHalfFOV;
#####     m[1][2] = 0.0f;
#####     m[1][3] = 0.0f;

#####     m[2][0] = 0.0f;
#####     m[2][1] = 0.0f;
#####     m[2][2] = (-zNear - zFar) / zRange;
#####     m[2][3] = 2.0f * zFar * zNear / zRange;

#####     m[3][0] = 0.0f;
#####     m[3][1] = 0.0f;
#####     m[3][2] = 1.0f;
#####     m[3][3] = 0.0f; 
##### }
#### В функции отрисовки
##### Pipeline p;
##### // Меняем масштаб
##### p.Scale(0.1f, 0.1f, 0.1f);
##### // Вращаем фигуру
##### p.Rotate(0, Scale, 0);
##### // Устанавливаем положение фигуры
##### p.WorldPos(0.0f, 0.0f, 100.0f);
##### // Задаём проекцию перспективы
##### p.SetPerspectiveProj(90.0f, WINDOW_WIDTH, WINDOW_HEIGHT, 10.0f, 10000.0f);
##### // Загружаем данные в uniform - переменные шейдера(адрес переменной, количество матриц,
##### // передаётся ли матрица по строкам, указатель на первый элемент матрицы)
##### glUniformMatrix4fv(gWorldLocation, 1, GL_TRUE, (const GLfloat*)p.getTransformation());
##### В методе getTransformation добавляем в умножение матрицу перспективы:
##### m_transformation = PersProjTrans * TranslationTrans * RotateTrans * ScaleTrans;
#### Также необходимо задать параметры окна
##### // Задаём параметры окна
##### #define WINDOW_WIDTH 1366
##### #define WINDOW_HEIGHT 768
### Вывод
#### Вданной лабораторной работе были получены навыки по работе с перемещением, преобразовыванием и изменением масштаба объекта
