# Sudoku-Solver
global sudoku, suspects

# Sudoku grid to be solved:
sudoku = [
    ['4', ' ', '6', '5', ' ', '2', '8', ' ', '9'],
    [' ', ' ', ' ', ' ', '4', ' ', ' ', '3', ' '],
    [' ', ' ', ' ', ' ', ' ', ' ', ' ', ' ', '5'],
    ['6', ' ', ' ', '8', ' ', ' ', '1', ' ', ' '],
    ['5', ' ', ' ', ' ', '7', ' ', ' ', '8', ' '],
    ['3', ' ', '2', '9', ' ', '4', ' ', '6', ' '],
    [' ', '2', ' ', '6', ' ', ' ', ' ', ' ', '1'],
    [' ', ' ', ' ', ' ', '5', '3', '9', '4', ' '],
    ['8', '3', ' ', ' ', '9', ' ', ' ', ' ', '2']
]
suspects = {}
"""
The method that has been implemented in this solver is using 'suspects' for a given empty square in the sudoku grid.
- Whenever an empty grid square is encountered, its row, column and 3x3 sub-grid are checked.
- The union of the elements in the row, column and sub-grid forms the 'delete' set.
- The elements present in the set {1, 2, 3, ..., 9} (i.e. all possible values that any grid square can take) but not in the delete set are taken to be the 'suspects' for that square, i.e. the possible values it can take.
- After the suspects for every single square in the current grid are identified, the squares with only one suspect are filled with their respective suspects.
- This yields a new, simplified sudoku grid.
- These steps are repeated till the entire sudoku is solved.
"""

def initialise_suspects():
    # initialise_suspects() identifies the grid squares which are empty and adds them to the suspects dictionary.
    for i in range(9):
        for j in range(9):
            if sudoku[i][j] == ' ':
                suspects[(i, j)] = None

def initialise_boxes():
    # initialise_boxes defines sets which contain the elements of the 9 sub-grids of the sudoku.

    """
    The order of the 'boxes' is as follows:
    1 2 3
    4 5 6
    7 8 9
    """

    global box_1, box_2, box_3, box_4, box_5, box_6, box_7, box_8, box_9
    box_1 = {sudoku[i][j] for i in range(0, 3) for j in range(0, 3) if sudoku[i][j] != ' '}
    box_2 = {sudoku[i][j] for i in range(0, 3) for j in range(3, 6) if sudoku[i][j] != ' '}
    box_3 = {sudoku[i][j] for i in range(0, 3) for j in range(6, 9) if sudoku[i][j] != ' '}
    box_4 = {sudoku[i][j] for i in range(3, 6) for j in range(0, 3) if sudoku[i][j] != ' '}
    box_5 = {sudoku[i][j] for i in range(3, 6) for j in range(3, 6) if sudoku[i][j] != ' '}
    box_6 = {sudoku[i][j] for i in range(3, 6) for j in range(6, 9) if sudoku[i][j] != ' '}
    box_7 = {sudoku[i][j] for i in range(6, 9) for j in range(0, 3) if sudoku[i][j] != ' '}
    box_8 = {sudoku[i][j] for i in range(6, 9) for j in range(3, 6) if sudoku[i][j] != ' '}
    box_9 = {sudoku[i][j] for i in range(6, 9) for j in range(6, 9) if sudoku[i][j] != ' '}

def fill_suspects():
    global box_1, box_2, box_3, box_4, box_5, box_6, box_7, box_8, box_9

    for element in suspects:
        row, column = tuple(element)

        row_elements = {sudoku[row][k] for k in range(0, 9) if sudoku[row][k] != ' '}
        # row_elements is the set containing the elements present in the row of the blank square currently being considered.

        column_elements = {sudoku[k][column] for k in range(0, 9) if sudoku[k][column] != ' '}
        # column_elements is the set containing the elements present in the column of the blank square currently being considered.

        # box_elements is the set containing the elements present in the sub-grid of the blank square currently being considered.
        if row < 3 and column < 3:
            box_elements = box_1

        elif row < 3 and (column >= 3 and column < 6):
            box_elements = box_2

        elif row < 3 and (column >= 6 and column < 9):
            box_elements = box_3

        elif (row >= 3 and row < 6) and column < 3:
            box_elements = box_4

        elif (row >= 3 and row < 6) and (column >= 3 and column < 6):
            box_elements = box_5

        elif (row >= 3 and row < 6) and (column >= 6 and column < 9):
            box_elements = box_6

        elif (row >= 6 and row < 9) and column < 3:
            box_elements = box_7

        elif (row >= 6 and row < 9) and (column >= 3 and column < 6):
            box_elements = box_8

        elif (row >= 6 and row < 9) and (column >= 6 and column < 9):
            box_elements = box_9

        delete_set = (row_elements | column_elements | box_elements)
        # delete_set is the union of the aforementioned sets.
        # '|' is the python set union operator.

        suspects[element] = {'1', '2', '3', '4', '5', '6', '7', '8', '9'} - delete_set

def fill_one_suspect_elements():
    # fill_one_suspect_elements() adds to the sudoku the values for the blank squares having only one suspect.
    for element in suspects:
        if len(suspects[element]) == 1:
            sudoku[tuple(element)[0]][tuple(element)[1]] = list(suspects[element])[0]

def is_complete():
    # is_complete() checks whether the sudoku has reached the solved state or not.
    for row in sudoku:
        for element in row:
            if element == ' ':
                return False
    return True

while(is_complete() == False):
    suspects.clear() # The suspects dictionary is cleared before every iteration as if it were not, the previously stored elements would interfere with finding the suspects for the simplified grid.
    initialise_suspects()
    initialise_boxes()
    fill_suspects()
    fill_one_suspect_elements()

for row in sudoku:
    print(row)
