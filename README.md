# Examen-P1
Luis Enrique Diaz

function gui_robot_cinematica()
    % Ventana clásica ampliada a 1280px para dar espacio al panel de procedimiento
    fig = figure('Name', 'Cinemática Directa - Robot 4 DOF', ...
                 'NumberTitle', 'off', ...
                 'Units', 'pixels', ...
                 'Position', [100, 100, 1280, 620], ...
                 'Color', [0.94 0.94 0.94], ...
                 'MenuBar', 'none', ...
                 'Resize', 'on');

    % --- ÁREA DE DIBUJO 3D (IZQUIERDA) ---
    ax = axes('Parent', fig, 'Units', 'pixels', 'Position', [35, 65, 560, 520]);

    % --- PANEL DE CONTROL (CENTRO) ---
    panel = uipanel('Parent', fig, 'Title', 'Parámetros de Entrada', ...
                    'Units', 'pixels', 'Position', [610, 15, 300, 585], ...
                    'FontWeight', 'bold');

    % LONGITUDES
    uicontrol('Parent', panel, 'Style', 'text', 'String', 'Longitudes de Eslabones (l):', ...
        'Position', [10, 540, 260, 20], 'FontWeight', 'bold', 'HorizontalAlignment', 'left');

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'l1:', 'Position', [10, 510, 30, 20]);
    txt_l1 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '10', 'Position', [45, 510, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'l2:', 'Position', [145, 510, 30, 20]);
    txt_l2 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '8', 'Position', [180, 510, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'l3:', 'Position', [10, 475, 30, 20]);
    txt_l3 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '6', 'Position', [45, 475, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'l4:', 'Position', [145, 475, 30, 20]);
    txt_l4 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '4', 'Position', [180, 475, 80, 25]);

    % ÁNGULOS
    uicontrol('Parent', panel, 'Style', 'text', 'String', 'Ángulos Articulares (°):', ...
        'Position', [10, 430, 260, 20], 'FontWeight', 'bold', 'HorizontalAlignment', 'left');

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'θ1:', 'Position', [10, 400, 30, 20]);
    txt_q1 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '45', 'Position', [45, 400, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'θ2:', 'Position', [145, 400, 30, 20]);
    txt_q2 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '30', 'Position', [180, 400, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'θ3:', 'Position', [10, 365, 30, 20]);
    txt_q3 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '-20', 'Position', [45, 365, 80, 25]);

    uicontrol('Parent', panel, 'Style', 'text', 'String', 'θ4:', 'Position', [145, 365, 30, 20]);
    txt_q4 = uicontrol('Parent', panel, 'Style', 'edit', 'String', '15', 'Position', [180, 365, 80, 25]);

    % CHECKBOX
    chk_metodo = uicontrol('Parent', panel, 'Style', 'checkbox', ...
        'String', ' Usar Matrices Homogéneas (DH)', ...
        'Position', [10, 320, 260, 25], 'FontWeight', 'bold', 'Value', 0);

    % BOTÓN Y RESULTADOS
    btn_calcular = uicontrol('Parent', panel, 'Style', 'pushbutton', ...
        'String', 'Calcular y Graficar', 'Position', [10, 265, 260, 40], ...
        'BackgroundColor', [0.2 0.5 0.9], 'ForegroundColor', [1 1 1], ...
        'FontWeight', 'bold', 'FontSize', 10);

    panel_res = uipanel('Parent', panel, 'Title', 'Posición Efector Final (EF)', ...
        'Units', 'pixels', 'Position', [10, 10, 260, 230], 'FontWeight', 'bold');

    lbl_ef_x = uicontrol('Parent', panel_res, 'Style', 'text', 'String', 'EF_x = --', ...
        'Position', [10, 150, 240, 25], 'FontSize', 11, 'FontWeight', 'bold', 'HorizontalAlignment', 'left');

    lbl_ef_y = uicontrol('Parent', panel_res, 'Style', 'text', 'String', 'EF_y = --', ...
        'Position', [10, 110, 240, 25], 'FontSize', 11, 'FontWeight', 'bold', 'HorizontalAlignment', 'left');

    lbl_ef_z = uicontrol('Parent', panel_res, 'Style', 'text', 'String', 'EF_z = --', ...
        'Position', [10, 70, 240, 25], 'FontSize', 11, 'FontWeight', 'bold', 'HorizontalAlignment', 'left');

    lbl_metodo = uicontrol('Parent', panel_res, 'Style', 'text', 'String', 'Método: Geométrico', ...
        'Position', [10, 20, 240, 25], 'FontSize', 9, 'FontAngle', 'italic', 'HorizontalAlignment', 'left');

    % --- PANEL DE PROCEDIMIENTO Y ECUACIONES (DERECHA EXTREMA) ---
    panel_proc = uipanel('Parent', fig, 'Title', 'Procedimiento de Cálculo', ...
                         'Units', 'pixels', 'Position', [925, 15, 340, 585], ...
                         'FontWeight', 'bold');

    txt_proc = uicontrol('Parent', panel_proc, 'Style', 'edit', ...
        'Max', 2, 'Min', 0, ...
        'Position', [10, 10, 318, 545], ...
        'HorizontalAlignment', 'left', ...
        'FontName', 'Courier', ...
        'FontSize', 8.5, ...
        'BackgroundColor', [1 1 1]);

    % Asignar Callbacks / Eventos
    set(btn_calcular, 'Callback', @(~,~) calcular());
    set(chk_metodo, 'Callback', @(~,~) calcular());

    % Renderizar al iniciar
    calcular();

    % FUNCIÓN DE CÁLCULO Y GRAFICADO
    function calcular()
        l1 = str2double(get(txt_l1, 'String'));
        l2 = str2double(get(txt_l2, 'String'));
        l3 = str2double(get(txt_l3, 'String'));
        l4 = str2double(get(txt_l4, 'String'));

        q1_deg = str2double(get(txt_q1, 'String'));
        q2_deg = str2double(get(txt_q2, 'String'));
        q3_deg = str2double(get(txt_q3, 'String'));
        q4_deg = str2double(get(txt_q4, 'String'));

        q1 = deg2rad(q1_deg);
        q2 = deg2rad(q2_deg);
        q3 = deg2rad(q3_deg);
        q4 = deg2rad(q4_deg);

        usar_dh = get(chk_metodo, 'Value');

        str_proc = {};

        if usar_dh
            % MATRICES HOMOGÉNEAS 
            set(lbl_metodo, 'String', 'Método: Matrices Homogéneas (DH)');

            A1 = [cos(q1) 0 sin(q1) 0; sin(q1) 0 -cos(q1) 0; 0 1 0 l1; 0 0 0 1];
            A2 = [cos(q2) -sin(q2) 0 l2*cos(q2); sin(q2) cos(q2) 0 l2*sin(q2); 0 0 1 0; 0 0 0 1];
            A3 = [cos(q3) -sin(q3) 0 l3*cos(q3); sin(q3) cos(q3) 0 l3*sin(q3); 0 0 1 0; 0 0 0 1];
            A4 = [cos(q4) -sin(q4) 0 l4*cos(q4); sin(q4) cos(q4) 0 l4*sin(q4); 0 0 1 0; 0 0 0 1];

            T1 = A1; T2 = T1*A2; T3 = T2*A3; T4 = T3*A4;

            P0 = [0; 0; 0];
            P1 = T1(1:3, 4);
            P2 = T2(1:3, 4);
            P3 = T3(1:3, 4);
            P4 = T4(1:3, 4);

            % Texto del procedimiento DH
            str_proc{end+1} = '=== MÉTODO MATRICES DH ===';
            str_proc{end+1} = ' ';
            str_proc{end+1} = '1. Matriz A1 (θ1, l1):';
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A1(1,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A1(2,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A1(3,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A1(4,:));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '2. Matriz A2 (θ2, l2):';
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A2(1,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A2(2,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A2(3,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A2(4,:));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '3. Matriz A3 (θ3, l3):';
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A3(1,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A3(2,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A3(3,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A3(4,:));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '4. Matriz A4 (θ4, l4):';
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A4(1,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A4(2,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A4(3,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', A4(4,:));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '5. Matriz T0_4 = A1*A2*A3*A4:';
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', T4(1,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', T4(2,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', T4(3,:));
            str_proc{end+1} = sprintf('[%6.2f %6.2f %6.2f %6.2f]', T4(4,:));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '6. Posición Final (Columna 4):';
            str_proc{end+1} = sprintf('P4 = [%.4f, %.4f, %.4f]', P4(1), P4(2), P4(3));

        else
            % MÉTODO GEOMÉTRICO
            set(lbl_metodo, 'String', 'Método: Geométrico');

            P0 = [0; 0; 0];
            P1 = [0; 0; l1];

            r2 = l2 * cos(q2);
            z2 = l1 + l2 * sin(q2);
            P2 = [r2 * cos(q1); r2 * sin(q1); z2];

            r3 = r2 + l3 * cos(q2 + q3);
            z3 = z2 + l3 * sin(q2 + q3);
            P3 = [r3 * cos(q1); r3 * sin(q1); z3];

            r4 = r3 + l4 * cos(q2 + q3 + q4);
            z4 = z3 + l4 * sin(q2 + q3 + q4);
            P4 = [r4 * cos(q1); r4 * sin(q1); z4];

            % Texto del procedimiento Geométrico
            str_proc{end+1} = '=== MÉTODO GEOMÉTRICO ===';
            str_proc{end+1} = ' ';
            str_proc{end+1} = '1. Ángulos en Radianes:';
            str_proc{end+1} = sprintf('θ1 = %.1f° -> %.4f rad', q1_deg, q1);
            str_proc{end+1} = sprintf('θ2 = %.1f° -> %.4f rad', q2_deg, q2);
            str_proc{end+1} = sprintf('θ3 = %.1f° -> %.4f rad', q3_deg, q3);
            str_proc{end+1} = sprintf('θ4 = %.1f° -> %.4f rad', q4_deg, q4);
            str_proc{end+1} = ' ';
            str_proc{end+1} = '2. Coordenadas P0 y P1:';
            str_proc{end+1} = sprintf('P0 = [0.0, 0.0, 0.0]');
            str_proc{end+1} = sprintf('P1 = [0.0, 0.0, %.2f]', l1);
            str_proc{end+1} = ' ';
            str_proc{end+1} = '3. Coordenadas P2:';
            str_proc{end+1} = sprintf('r2 = l2*cos(θ2) = %.2f*cos(%.2f) = %.3f', l2, q2, r2);
            str_proc{end+1} = sprintf('z2 = l1 + l2*sin(θ2) = %.3f', z2);
            str_proc{end+1} = sprintf('P2 = [%.3f, %.3f, %.3f]', P2(1), P2(2), P2(3));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '4. Coordenadas P3:';
            str_proc{end+1} = sprintf('r3 = r2 + l3*cos(θ2+θ3) = %.3f', r3);
            str_proc{end+1} = sprintf('z3 = z2 + l3*sin(θ2+θ3) = %.3f', z3);
            str_proc{end+1} = sprintf('P3 = [%.3f, %.3f, %.3f]', P3(1), P3(2), P3(3));
            str_proc{end+1} = ' ';
            str_proc{end+1} = '5. Coordenadas P4 (Efector Final):';
            str_proc{end+1} = sprintf('r4 = r3 + l4*cos(θ2+θ3+θ4) = %.3f', r4);
            str_proc{end+1} = sprintf('z4 = z3 + l4*sin(θ2+θ3+θ4) = %.3f', z4);
            str_proc{end+1} = sprintf('P4 = [%.4f, %.4f, %.4f]', P4(1), P4(2), P4(3));
        end

        % Actualizar texto del cuadro de procedimiento
        set(txt_proc, 'String', str_proc);

        EF_x = P4(1); EF_y = P4(2); EF_z = P4(3);

        set(lbl_ef_x, 'String', sprintf('EF_x = %.4f', EF_x));
        set(lbl_ef_y, 'String', sprintf('EF_y = %.4f', EF_y));
        set(lbl_ef_z, 'String', sprintf('EF_z = %.4f', EF_z));

        % DIBUJO DEL ROBOT
        cla(ax);
        hold(ax, 'on');

        % Eslabones y articulaciones
        P = [P0, P1, P2, P3, P4];
        plot3(ax, P(1,:), P(2,:), P(3,:), '-o', 'LineWidth', 3, ...
            'Color', [0.1 0.3 0.8], 'MarkerSize', 6, 'MarkerFaceColor', [1 0.5 0]);

        % Base fija
        plot3(ax, 0, 0, 0, '^', 'MarkerSize', 10, 'MarkerFaceColor', 'k', 'Color', 'k');

        % CONSTRUCCIÓN DE LA PINZA
        phi = q2 + q3 + q4; % Ángulo total acumulado de inclinación
        ux = cos(phi) * cos(q1);
        uy = cos(phi) * sin(q1);
        uz = sin(phi);

        vx = -sin(q1);
        vy = cos(q1);
        vz = 0;

        ancho_pinza = 1.0;
        largo_dedos  = 1.5;

        % Puntos del perfil en U
        p_u1 = P4 + largo_dedos*[ux; uy; uz] + (ancho_pinza/2)*[vx; vy; vz];
        p_u2 = P4 + (ancho_pinza/2)*[vx; vy; vz];
        p_u3 = P4 - (ancho_pinza/2)*[vx; vy; vz];
        p_u4 = P4 + largo_dedos*[ux; uy; uz] - (ancho_pinza/2)*[vx; vy; vz];

        pinza = [p_u1, p_u2, p_u3, p_u4];

        % Graficar la pinza
        plot3(ax, pinza(1,:), pinza(2,:), pinza(3,:), '-k', 'LineWidth', 3);
        plot3(ax, EF_x, EF_y, EF_z, 'o', 'MarkerSize', 5, 'MarkerFaceColor', 'g', 'Color', 'g');

        grid(ax, 'on');
        axis(ax, 'equal');
        xlabel(ax, 'Eje X'); ylabel(ax, 'Eje Y'); zlabel(ax, 'Eje Z');
        title(ax, 'Configuración 3D del Robot');
        view(ax, 45, 30);

        alcance_max = max(1, l1 + l2 + l3 + l4);
        xlim(ax, [-alcance_max, alcance_max]);
        ylim(ax, [-alcance_max, alcance_max]);
        zlim(ax, [0, alcance_max + 2]);

        legend(ax, {'Eslabones', 'Base', 'Pinza (EF)'}, 'Location', 'northeast');
        hold(ax, 'off');
    end
end
