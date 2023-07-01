# C
//1 зад (20т) Създайте структура Course, която да съхранява информация за
//курс, планиран за провеждане в учебния център. Съхраняваната информация
//за всеки курс е следната:
//- Име на курса низ (с дължина 50 символа)
//- Начална дата (низ с фиксирана дължина и формат ГГГГ,ММ,ДД)
//- Общ брой лекции (цяло число)
//-Цена (реално число с формат 0.00
//Данните се съхраняват в бинарен файл с име courses.bin. Прочетете данните
//от файла и ти заредете в динамичен масив courses с елементи от тип
//структурата Course, а данните за актуалния брой курсове запазете в
//целочислена променлива n. Обработвайте грешки, възникнали при работа с
//бинарния файл.
//*Приемете, че файлът вече е създаден и попълнен с данни, като съдържа броя
//на планираните курсове, последван от самите курсове

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
typedef struct {
char name[51];
char start_date[11];
int total_lectures;
float price;
} Course;
int main() {
FILE* file = fopen("courses.bin", "rb");
if (file == NULL) {
printf("failed to open courses.bin file \n");
}
int n;
fread(&n, sizeof(int), 1, file); 
if (n <= 0) {
printf("not valid data for 'n'\n");
fclose(file);
}
Course* courses = (Course*)malloc(n * sizeof(Course));
if (courses == NULL) {
printf("error with allocation memory save\n");
fclose(file);
}
fread(courses, sizeof(Course), n, file); 
fclose(file);
for (int i = 0; i < n; i++) {
printf("Course Name: %s\n", courses[i].name);
printf("Start Date: %s\n", courses[i].start_date);
printf("Total Lectures: %d\n", courses[i].total_lectures);
printf("Price: %.2f\n", courses[i].price);
printf("-----------------\n");
}
free(courses); 
return 0;
}


//2 зад. (15г.) Дефинирайте функция, която приема като параметри масив с
//елементи от тип структурата Course, брой елементи на масива и целочислена
//стойност за индекс на елемент от динамичния масив. Функцията намалява
//цената с 10% на курса на съответната позиция в динамичния масив с цел
//промоционална кампания. Валидирайте, че стойността на идекса е в границите
//на масива Функцията принтира на екрана новата цена на курса името на курса
//и началната дата, в следния формат нова цена на курса» лв. име на курса»
//начална дата>
//пример: 719.19 - Въведение в програмирането - 2023.12.12
 
void decreases_price(Course* array, int count_elem_arr, int j_index_arr)
{
    if (j_index_arr < count_elem_arr) {
        double discount = array[j_index_arr].price * 0.10;
        double final_price = array[j_index_arr].price - discount;
        printf("%.2lf lv - %s - %s\n", final_price, array[j_index_arr].name_course, array[j_index_arr].start_date);
    }
    else {
        printf("index > count of elements in array");
    }
}
 //3 зад. (20т.) Дефинирайте функция, която получава като параметри масив с
//елементи от тип структурата Course, брой елементи на масива, минимална и
//максимална цена (реални числа с формат 0.00). Функцията записва в текстовия
//файл с име offer.txt информация за всички курсове с цена в посочения диапазон
//Обработвайте грешки, възникнали при работа с текстовия файл. Функцията
//връща броят на записаните елементи или 0, ако няма съвпадение
//Информацията се записва в следния формат:
//<име на курс>
//<начална дата>
//<брой лекции>
//<цена>
int write_text_file_price(Course* array2, int count_elem_arr, double min_price, double max_price) {
    FILE* fp_text = fopen("offer.txt", "a+");
    if (fp_text == NULL) {
        printf("Failed to open file");
        return 1;
    }
    int found = 0;
    for (int i = 0; i < count_elem_arr; i++) {
        if (min_price < array2[i].price && array2[i].price < max_price) {
            fprintf(fp_text, "%s,%s,%d,%.2lf\n", array2[i].name_course, array2[i].start_date, array2[i].count_lectures, array2[i].price);
            found += 1;
        }
    }
    fclose(fp_text);
    return found;
}
4 зад (20т.) Дефинирайте функция, която приема като параметри масив с
елементи от тип структурата Course, брой елементи на масива име на курс
начална дата (низ с фиксиран размер и формат ГГГГ.ММ.ДД). Функцията
изтрива курса със съответното име и начална дата от динамичния масив.
Функцията връща като резултат указател към началото на променения масив
или NULL, ако е възникнала грешка
 
Course* delete_course(Course* array3, int* count_element_arr, const char* name_course, const char* start_date) {
    int found = 0;
    for (int i = 0; i < *count_element_arr; i++) {
        if (strcmp(array3[i].name_course, name_course) == 0 && strcmp(array3[i].start_date, start_date) == 0) {
            for (int j = i; j < *count_element_arr - 1; j++) {
                array3[j] = array3[j + 1];
            }
            (*count_element_arr)--;
            Course* new_array3 = (Course*)realloc(array3, (*count_element_arr) * sizeof(Course));
            if (new_array3 == NULL) {
                perror("Error on allocating memory.");
                exit(1);
            }
            if (new_array3 != array3) {
                free(array3);
                array3 = new_array3;
            }
            else {
                new_array3 = NULL;
            }
            FILE* fp_bin = fopen("courses.bin", "wb");
            if (fp_bin == NULL) {
                printf("Failed to open file");
                free(array3);
                exit(1);
            }
            for (int k = 0; k < *count_element_arr; k++) {
                fprintf(fp_bin, "%s,%s,%d,%.2lf\n", array3[k].name_course, array3[k].start_date, array3[k].count_lectures, array3[k].price);
            }
            fclose(fp_bin);
            found = 1;
            break;
        }
    }
    if (found == 0) {
        return NULL;  
    }
    return array3;  
}
